# Android多线程通信-Handler机制

Message：消息分为硬件产生的消息(如按钮、触摸)和软件生成的消息；
MessageQueue：消息队列（按时间的优先级队列）的主要功能向消息池投递消息(MessageQueue.enqueueMessage)和取走消息池的消息(MessageQueue.next)；
Handler：消息辅助类，主要功能向消息池发送各种消息事件(Handler.sendMessage)和处理相应消息事件(Handler.handleMessage)；
Looper：不断循环执行(Looper.loop)，按分发机制将消息分发给目标处理者。







