### PacketWrapper應用
* `ActionBar`(_`WrapperPlayServerChat`_)
  ```Java
  WrapperPlayServerChat wpsc = new WrapperPlayServerChat();
  wpsc.setChatType(ChatType.GAME_INFO);
  wpsc.setMessage(WrappedChatComponent.fromText(message));
  wpsc.sendPacket(p);
  ```
