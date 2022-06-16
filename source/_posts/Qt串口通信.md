---
title: Qt串口通信
date: 2022-06-16 19:57:19
tags:
    - C/C++
    - Qt
    - 串口
toc: true
categories: C/C++
---

使用QSerialPort和QSerialPortInfo,对日常使用进行了封装。

在实际使用Qt串口过程中，出现多次触发readyRead()信号，一次性使用readAll()并不能获取所有数据，可以通过修改通信协议，在本封装中采用的是延时，在接受到readyRead信号时进行倒计时，在倒计时结束之前，将接收到的信号存入一个缓冲区。

头文件
```c++
/*
 * serial.h
 * 创建时间： 2022/5/9
 * 描述: 封装串口通信。
 *
 * 修改时间: 2022/5/23
 * 修改描述:
 *          添加readReady()可修改时长接口
 *
 * 修改时间： 2022/5/9
 * 修改描述：
 *          Qt在大量数据通信时会多次触发readyRead()信号，
 *      本文件使用了QTimer在第一次触发readyRead()信号时计
 *      时，每次触发的readyRead()信号添加到临时缓冲区，当
 *      计时终止时，将临时缓冲区内的内容复制给缓冲区，清空
 *      临时缓冲区。延时时间请根据实际情况进行修改
 */
#ifndef SERIAL_H
#define SERIAL_H

#include <QObject>
#include <QSerialPort>
#include <QSerialPortInfo>
#include <QMessageBox>
#include <QTimer>

class Serial:public QObject
{
    Q_OBJECT

public:
    Serial();
    bool isOpen = false;                            // 串口是否已打开
    QStringList scanSerial();                       // 扫描可用串口
    QStringList standardBaudRates();                // 获取标准波特率列表

    bool open(QString serialName, int baudRate,QSerialPort::DataBits databits=QSerialPort::Data8,
            QSerialPort::Parity parity = QSerialPort::NoParity,QSerialPort::StopBits stopBits=QSerialPort::OneStop,
              QSerialPort::FlowControl flowControl=QSerialPort::NoFlowControl);    // 打开串口
    void close();                                   // 关闭串口
    void sendData(QByteArray &sendData);            // 发送数据给下位机
    QByteArray getReadBuf();                        // 获得读取数据缓冲区
    void clearReadBuf();                            // 清除读取数据缓冲区

    void setReadTime(int mesc);                     // 设置读取时长
    int getReadTime();                              // 获取当前读取时长

signals:
    void readSignal();                              // 当下位机中有数据发送过来触发该信号
    void readBufSignal();                           // 计时器终止触发

public slots:
    void readData();                                // 读取数据
    void timerUpdate();

private:
    QSerialPort *m_serialPort;                      // 实例化一个指向串口的指针，可以用于访问串口
    QSerialPortInfo *m_serialPortInfo;              // 实例化QSerialPortInfo
    QByteArray m_tmpBuf;                            // 临时存储下位机发来数据，因为大量数据通信Qt会多次分发
    QByteArray m_readBuf;                           // 返回读取的数据缓冲区
    QTimer *m_timer;                                // 时间对象
    int readTime;                                   // 缓冲区读取时长
};

#endif // SERIAL_H

```


```c++
#include "serial.h"

Serial::Serial()
{
    // 初始化串口
    m_serialPort = new QSerialPort;
    m_serialPortInfo = new QSerialPortInfo;
    m_timer = new QTimer(this);

    // 当计时终止，处理读取到的数据，发送读取数据信号
    connect(m_timer,SIGNAL(timeout()),this,SLOT(timerUpdate()));
    connect(m_timer,SIGNAL(timeout()),this,SIGNAL(readBufSignal()));
 }

/**
 * @brief Serial::scanSerial
 * 扫描可用串口
 * @return
 */
QStringList Serial::scanSerial()
{
    QStringList serialStrList;

    // 读取串口信息
    foreach(const QSerialPortInfo &info, QSerialPortInfo::availablePorts())
    {
        QSerialPort serial;
        serial.setPort(info);
        if(serial.open(QIODevice::ReadWrite)){
            serialStrList.append(info.portName());
        }
        serial.close();
    }

    return serialStrList;
}

/**
 * @brief Serial::open
 *
 * 打开串口
 * @param serialName 串口名
 * @param baudRate 波特率
 * @param databits  数据位
 * @param parity    校验位
 * @param stopBits  停止位
 * @param flowControl   流控制
 * @return
 */
bool Serial::open(QString serialName, int baudRate, QSerialPort::DataBits databits, QSerialPort::Parity parity, QSerialPort::StopBits stopBits, QSerialPort::FlowControl flowControl)
{
    // 设置串口名
    m_serialPort->setPortName(serialName);
    // 打开串口(以读写方式)
    if(m_serialPort->open(QIODevice::ReadWrite))
    {
        m_serialPort->setBaudRate(baudRate); // 设置波特率
        m_serialPort->setDataBits(databits); // 设置数据位(数据位为8位)
        m_serialPort->setParity(parity); // 设置校验位(无校验位)
        m_serialPort->setStopBits(stopBits); // 设置停止位(停止位为1)
        m_serialPort->setFlowControl(flowControl); // 设置流控制(无数据流控制)

        // 当下位机中有数据发送过来时就会响应这个槽函数
        connect(m_serialPort, SIGNAL(readyRead()), this, SLOT(readData()));
        // 当下位机中有数据发送过来时就会触发这个信号，以提示其它类对象
        connect(m_serialPort, SIGNAL(readyRead()), this, SIGNAL(readSignal()));

        return true;
    }

    return false;
}

/**
 * @brief Serial::close
 * 关闭串口
 */
void Serial::close()
{
    m_serialPort->clear();
    m_serialPort->close();
}

/**
 * @brief Serial::standardBaudRates
 *
 * 返回QStringList格式的波特率列表
 * @return
 */
QStringList Serial::standardBaudRates()
{
    QStringList sBRs;
    foreach(qint32 baud,m_serialPortInfo->standardBaudRates()){
        sBRs << QString::number(baud);
    }
    return sBRs;
}

/**
 * @brief Serial::sendData
 * 发送数据给下位机
 * @param sendData
 */
void Serial::sendData(QByteArray &sendData)
{
    // 发送数据帧
    m_serialPort->write(sendData);
}

/**
 * @brief Serial::readData
 * 读取从下位机发来的数据
 */
void Serial::readData()
{
    // 将下位机发来数据存储在数据缓冲区
    m_timer->start(readTime);
    m_tmpBuf.append(m_serialPort->readAll());
}

/**
 * @brief Serial::getReadBuf
 * 返回缓冲区数据
 * @return
 */
QByteArray Serial::getReadBuf()
{
    return m_readBuf;
}

/**
 * @brief Serial::clearReadBuf
 * 清空缓冲区
 */
void Serial::clearReadBuf()
{
    m_readBuf.clear();
}

/**
 * @brief Serial::timerUpdate
 * QTimer计时结束触发，复制并清空临时缓冲区
 */
void Serial::timerUpdate()
{
    m_timer->stop();
    m_readBuf = m_tmpBuf;
    m_tmpBuf.clear();
}

/**
 * @brief Serial::setReadTime
 * 设置读取时长
 * @param mesc
 */
void Serial::setReadTime(int mesc)
{
    readTime = mesc;
}

/**
 * @brief Serial::getReadTime
 * 获取当前读取时长
 * @return
 */
int Serial::getReadTime()
{
    return readTime;
}

```