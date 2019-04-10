### PacketWrapper應用
> 相關連結：
> * [ProtocolLib wiki][pro_wiki]
> * [ProtocolLib github][pro_git]
> * [PacketWrapper github][pw_git]

* `ActionBar`(_`WrapperPlayServerChat`_)
  ```Java
  WrapperPlayServerChat wpsc = new WrapperPlayServerChat();
  wpsc.setChatType(ChatType.GAME_INFO);
  wpsc.setMessage(WrappedChatComponent.fromText(message));
  wpsc.sendPacket(p);
  ```

[pro_wiki]: https://wiki.vg/Protocol
[pro_git]: https://github.com/dmulloy2/ProtocolLib/
[pw_git]: https://github.com/dmulloy2/PacketWrapper
