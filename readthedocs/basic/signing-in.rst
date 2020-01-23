.. _登录:

==========
登录
==========

在使用Telegram's API进行开发前, 你必须获得个人的API ID and hash:

1. `访问<https://my.telegram.org/>`_ 并使用电话号码登录开发者账户

2. 点击‘API Development’

3. 一个*Create new application* 的窗口会出现，填写你的应用细节，此处不需要任何的*URL*，
   只有前两个区域(*App title* and *Short name*)事后可以更改。
   
4. 在底端点击*Create application*按钮。记住，你的**API hash 是非常私密的东西**，
   Telegrem官方不会提供你取消它的机会，不要在任何地方展示他。

.. 注意::
    
    *您的应用程序*使用的是这个API ID和hash，而不是您的电话号码。您可以使用这个
    API ID和hash与*任何*电话号码甚至是机器人账户。


编辑代码
================


这是给Python新手程序员作的一个简单介绍。
我们将在``hello.py``里写一些代码，你也可以用任何你喜欢的文本编辑器来编辑，
要运行这段代码，在终端中输入``python3 hello.py``命令并回车。

.. 重要提示::
    不要将你的脚本文件命名为``telethon.py``！Python程序将会出现引入失败的情况，
    例如 "ImportError: cannot import name 'TelegramClient' ...".


登录
==========

我们最终来写一些代码来登录你的Telegram账户。

.. code-block:: python

    from telethon import TelegramClient

    #这里请使用你自己的值
    api_id = 12345
    api_hash = '0123456789abcdef0123456789abcdef'

    # 第一个参数是后缀为.session的文件文件名(允许绝对路径)
    with TelegramClient('anon', api_id, api_hash) as client:
        client.loop.run_until_complete(client.send_message('me', 'Hello, myself!'))



在第一行，我们从telethon库中引入了TelegramClient类，来创建一个client实例。
然后，我们定义了用来存储我们的API ID和hash的变量。


最后，我们创建了一个新的`TelegramClient <telethon.client.telegramclient.TelegramClient>`实例，
并将其赋值给变量client，现在我们可以使用client变量来做我们想做的事，比如给我们自己发送消息。

.. 注意::
    鉴于Telethon是一个异步库，你需要``等待``协程函数运行返回结果，不然，你需要使用循环结构来等待直到返回消息。在这个小小的例子中，我们不急着使用
    ``async def main()``结构。
    
    See :ref:`mastering-asyncio` to find out more.
    查看:ref:`mastering-asyncio`以了解更多。


``with``结构是使用Telethon库的首选。它将自动执行`start() <telethon.client.auth.AuthMethods.start>`，以实现自动登录。
如果session文件已经存在，它不会再次登录，所以注意你是否删除了这个文件。



以Bot账户方式登录
===========================


Telethon库也可以用于Telegram bot（一般bot账户，非用户），你仍然只需要API ID 和 hash ，但是代码上是非常相似的：

.. code-block:: python

    from telethon.sync import TelegramClient

    api_id = 12345
    api_hash = '0123456789abcdef0123456789abcdef'
    bot_token = '12345:0123456789abcdef0123456789abcdef

    # We have to manually call "start" if we want an explicit bot token
    # 如果我们需要一个显示的bot令牌，我们需要手动启动它。
    
    bot = TelegramClient('bot', api_id, api_hash).start(bot_token=bot_token)

    # But then we can use the client instance as usual
    # 但是之后，我们可以像往常一样使用client实例了。
    with bot:
        ...



要获得一个bot账户，你需要和`@BotFather <https://t.me/BotFather>`_对话。


通过代理方式登录
=========================


如果你需要通过代理来连接Telegram，你需要先安装PySocks库，然后作出一些更改。
.. code-block:: python

    TelegramClient('anon', api_id, api_hash)

改为

.. code-block:: python

    TelegramClient('anon', api_id, api_hash, proxy=(socks.SOCKS5, '127.0.0.1', 4444))


当然，你要把其中的IP和端口改为你自己的真实代理IP和相应端口。参数``proxy=`的值的数据类型是
tuple(集合)、list（列表）、dict（字典），就是PySocks库能接受的数据类型。


.. __: https://github.com/Anorov/PySocks#installation
.. __: https://github.com/Anorov/PySocks#usage-1



使用MTProto代理
=====================

MTProto代理是Telegram专属代理,工作方式与其他代理有些许不同，以下几种协议都是可行的:

* ``ConnectionTcpMTProxyAbridged``
* ``ConnectionTcpMTProxyIntermediate``
* ``ConnectionTcpMTProxyRandomizedIntermediate`` (preferred)


现在，如果你想使用MTProto代理，你需要手动指定连接方式，你的代理应该像这样：
.. code-block:: python

    from telethon import TelegramClient, connection
    #   我们需要改变连接方式 ^^^^^^^^^^

    client = TelegramClient(
        'anon',
        api_id,
        api_hash,

        # 使用其中一种可用的连接模式.
        # 一般情况下，这对大部分代理都可用.

        connection=connection.ConnectionTcpMTProxyRandomizedIntermediate,

        #   然后，使用集合来传递代理详细参数：
        #     (代理地址，端口，密码)
        #
        # 如果代理没有设置密码，那么密码设置应该像这样：
        #     '00000000000000000000000000000000'
        #     '00000000000000000000000000000000'
        proxy=('mtproxy.example.com', 2002, 'secret')
    )


在将来的更新中，我们会使MTProto代理更加容易使用，比如避免需要手动传递连接方式（``connection=``）。

简易简而言之，就是上面相同的代码，没有注释，使其更清晰：

.. code-block:: python

    from telethon import TelegramClient, connection

    client = TelegramClient(
        'anon', api_id, api_hash,
        connection=connection.ConnectionTcpMTProxyRandomizedIntermediate,
        proxy=('mtproxy.example.com', 2002, 'secret')
    )
