
How the dialogue states work:

each NPC has a dialogue state. Whenever player interacts  with the NPC and initiates a conversation, that is the dialogue state that 
plays automatically. You can set that state using SET_STATE side-effect. On top of that, the dialogues can be initiated by the NPC. 
This will happen in response to various events in the world. For example if you kill a friendly NPC, a guard will turn hostile, run 
up to you and initiate an arrest dialogue. More generally there can be various events like waking up/entering an area/leveling 
up/reaching an achievement/playing a cut scene. In response to such events an NPC might approach you and strike a conversation with you. 
Various events can also alter the NPC's dialogue state.


If you guys write in the following format I could automatically convert it into unreal

```
SOME_STATE
    Here is the text that the NPC says. 
    It can have multiple lines. 
    It's important that each line begins with 4 spaces. 
#   Lines that begin with # are ignored and can serve as comments
    The following line is blank and marks the beginning of player's responses
  
    - This line starts with 4 spaces and a dash. It will appear in the UI as a possible response (try to keep it as short as possible)
    - This is another alternative text for the same response. 
    - One of those
      This line starts with 6 spaces and this is the text that the main character will say when this response is
      selected. This line will also appear at the bottom of the screen. You can make it multi-line if you wish.
    * FIRST_SIDE_EFFECT side_effect_parameter:value
    * SECOND_SIDE_EFFECT side_effect_parameter1:value1 side_effect_parameter2:value2
    * THIRD_SIDE_EFFECT 
    < STATE_TO_TRANSITION_TO FLAG_TO_CHECK_IF_SET
    < ANOTHER_STATE_TO_TRANSITION_TO !FLAG_TO_CHECK_IF_NOT_SET

    - This is the first variant for the second response
    - This is the second variant for the second response  
      The full text for the second response. If the response doesn't transition into any new state (or none of the conditions matched), the dialogue ends.
```

The side effects currently implemented:

```
    * TRADE
    * EXCHANGE
    * FOLLOW
    * STOP_FOLLOWING
    * WAIT
    * STOP_WAITING
    * GIVE ITEM_ID:QUANTITY
    * GIVE_TO_NPC ITEM_ID:QUANTITY
    * SET FLAG_ID 
    * UNSET FLAG_ID
    * BREAK_DIALOGUE
 ```
 
The side effects are quite self explanatory. 

- TRADE allows you to trade with an NPC for money.
- EXCHANGE allows you to just freely exchange items without paying.
- FOLLOW makes the NPC follow the player.
- STOP_FOLLOWING makes the NPC stop being a follower and go back to roaming around.
- SET sets an arbitrary flag. You can just make up whatever flags you need
- WAIT causes the NPC to wait in the current location
- STOP_WAITING the NPC stops waiting and goes back to doing whatever it wants
- BREAK_DIALOGUE quits the dialogue. You can also quit if you don't specify any state to transition to

Some side effects that I will try to implement soon:

- PLAY ANIMATION_ID  (this includes sex scenes)
- SET_STAT STAT_ID:value (for example affection)
- TURN_HOSTILE TEAM_NUMBER (will be hostile towards characters of this team)
- TURN_FRIENDLY TEAM_NUMBER  (will be hostile towards characters of this team)
- SET_TEAM TEAM_NUMBER (two NPCs on the same team cannot hurt each other or by hurt be player in the same team)
- SET_STATE DIALOGUE_STATE (each NPC has a dialogue state. This will assign that state to the current NPC)

There are certain special built-in states that you can use

- AUTO is the default state that automatically generates options based on the current NPC's state. For example if the NPC is friendly you can tell them to follow you. If you choose that option and then enter dialogue again, you will see an option to tell the NPC to stop following you. The AUTO state does not have any fixed dialogue that the NPC says. Instead it is based on the NPCs mood, personality and other stats. Currently AUTO will generate the following options:
  - Follow me - if NPC is friendly and not following you
  - Wait here - if NPC is following you
  - Stop following me - if NPC is following you
  - Come with me again - if the NPC was waiting for you
  - 
- any state that starts with AUTO_ (for example AUTO_MY_CUSTOM_STATE) will work like a combination of AUTO and a custom state. This means that all the automatic responses will be generated plus additionally you can also add your own responses and set up your own text that NPC should say. 
