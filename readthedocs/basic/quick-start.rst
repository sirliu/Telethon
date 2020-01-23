===========
快速开始
===========

让我们来看一个长代码例子，以学习telethon库提供的几种方法。他们以友好著称，如有需要你将经常使用他们。

.. code-block:: python

    from telethon import TelegramClient

    # 请改成你自己的!
    api_id = 12345
    api_hash = '0123456789abcdef0123456789abcdef'
    client = TelegramClient('anon', api_id, api_hash)

    async def main():
        # 拉取你的个人信息
        me = await client.get_me()

        # "me" 是一个 User 对象. 你可以漂亮的打印任何具有"stringify"方法的Telegram对象:
        print(me.stringify())

        # 当你打印一些东西时, you see a representation of it.
        # 您可以使用点操作符访问Telegram对象的所有属性. 比如, 获取用户名:
        username = me.username
        print(username)
        print(me.phone)

        # 你可以打印所有你参与的对话:
        async for dialog in client.iter_dialogs():
            print(dialog.name, 'has ID', dialog.id)

        # 你可以给自己发信息...
        await client.send_message('me', 'Hello, myself!')
        # ...到某个聊天ID
        await client.send_message(-100123456, 'Hello, group!')
        # ...到你的联系人
        await client.send_message('+34600123123', 'Hello, friend!')
        # ...甚至是某个账户
        await client.send_message('TelethonChat', 'Hello, Telethon!')

        # 当然，您可以在消息中使用markdown:
        message = await client.send_message(
            'me',
            'This message has **bold**, `code`, __italics__ and '
            'a [nice website](https://lonamiwebs.github.io)!',
            link_preview=False
        )

        # 发送消息将返回您的已发送消息对象
        print(message.raw_text)

        # 如果有消息对象，可以直接回复消息
        await message.reply('Cool!')

        # 或者发送文件、歌曲、文档、相册...
        await client.send_file('me', '/home/me/Pictures/holidays.jpg')

        #您可以打印任何聊天的消息历史:
        async for message in client.iter_messages('me'):
            print(message.id, message.text)

            # 你也可以从信息中下载媒体!
            # 该方法将返回保存文件的路径.
            if message.photo:
                path = await message.download_media()
                print('File saved to', path)  # printed after download is done

    with client:
        client.loop.run_until_complete(main())


截止到这里，我们展示了如何登录，获取关于你自己的信息，发送
消息、文件、聊天、打印消息和下载文件。

您应该确保理解这里展示的代码，在自己开始写代码之前，你要注意如何调用各种方法等等。
稍后我们将看到所有可用的方法。

.. important::

    请注意，Telethon是一个异步库，因此，你应该这样做习惯它，
    并学习一点基本的 `asyncio`知识。这会很有帮助的。
    作为一个快速的开始，这意味着你通常需要在' ' async def ' '内写你的代码，像这样:

    .. code-block:: python

        client = ...

        async def do_something(me):
            ...

        async def main():
            # Most of your code should go here.
            # You can of course make and use your own async def (do_something).
            # They only need to be async if they need to await things.
            me = await client.get_me()
            await do_something(me)

        with client:
            client.loop.run_until_complete(main())

    理解了这些之后，你可以使用“telethon.sync”，如果你希望这样做(参见:ref: ' compatibility-and-convenience ')，
    但是请注意可以这样做 遇到其他问题(如iPython、Anaconda等)。
