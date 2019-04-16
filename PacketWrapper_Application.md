### PacketWrapper應用
> 相關連結：
> * [ProtocolLib wiki][pro_wiki]
> * [ProtocolLib github][pro_git]
> * [PacketWrapper github][pw_git]

* `Action bar`(_`WrapperPlayServerChat`_)
  ```Java
  WrapperPlayServerChat wpsc = new WrapperPlayServerChat();
  wpsc.setChatType(ChatType.GAME_INFO);
  wpsc.setMessage(WrappedChatComponent.fromText(message));
  wpsc.sendPacket(p);
  ```
* `Open book`(_`WrapperPlayServerCustomPayload`_)
  ```Java
  WrapperPlayServerCustomPayload wpscp = new WrapperPlayServerCustomPayload();
  ByteBuf bf = Unpooled.buffer(256);
  bf.setByte(0, (byte) 0);
  bf.writerIndex(1);
  wpscp.getHandle().getModifier().write(1, MinecraftReflection.getPacketDataSerializer(bf));
  wpscp.getHandle().getStrings().write(0, "MC|BOpen");
  wpscp.sendPacket(p);
  ```
* `Swing an item in main hand`(_`WrapperPlayServerAnimation`_)
  ```Java
  WrapperPlayServerAnimation wpsa = new WrapperPlayServerAnimation();
  wpsa.setEntityID(p.getEntityId());
  wpsa.setAnimation(0);
  wpsa.sendPacket(p);
  ```

[pro_wiki]: https://wiki.vg/Protocol
[pro_git]: https://github.com/dmulloy2/ProtocolLib/
[pw_git]: https://github.com/dmulloy2/PacketWrapper
