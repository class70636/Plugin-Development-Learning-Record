### 整數
類型 | 範圍 | 表達式 | 備註
:-: | :-: | :-: | :-:
所有整數 | ```{...-3,-2,-1,0,1,2,3,...}```|```-?[0-9]+``` | 若需包含可轉換至整數之小數在最後加```(\.0+)?```
自然數 | ```{0,1,2,3,4,...}``` | ```[0-9]+``` | 同上
正整數 | ```{1,2,3,4,...}``` | ```[1-9][0-9]*``` | 同上
負整數 | ```{...,-3,-2,-1}``` | ```-[1-9][0-9]*``` | 同上

### 小數
類型 | 範圍 | 表達式 | 備註
:-: | :-: | :-: | :-:
所有小數 | 負小數、0、正小數、整數|```-?[0-9]+(\.[0-9]+)?``` | -
「自然」小數 | >=0之(小數、整數) | ```[0-9]+(\.[0-9]+)?``` | -
正小數 | >0之(小數、整數) | ```[1-9][0-9]*(\.[0-9]+)?\|0\.(?!0+$)[0-9]+``` | -
負小數 | <0之(小數、整數) | ```-[1-9][0-9]*(\.[0-9]+)?\|0\.(?!0+$)[0-9]+``` | -
