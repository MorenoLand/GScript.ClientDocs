# Complete Packet ID Reference

This page is the complete numeric packet vocabulary used by the client protocol. The active runtime handler table can remap semantic actions, so always record both the numeric wire ID and the handler configuration that selected it.

The payload notes below are enum-level notes. Exact field breakdowns for the commonly decoded families are in [Payload Breakdowns](packet-breakdowns.md).

## Client to Game Server (PLI)

| ID | Name | Wire note |
| ---: | --- | --- |
| 0 | `PLI_LEVELWARP` | Levelwarp packet. |
| 1 | `PLI_BOARDMODIFY` | Boardmodify packet. |
| 2 | `PLI_PLAYERPROPS` | Playerprops packet. |
| 3 | `PLI_NPCPROPS` | Npcprops packet. |
| 4 | `PLI_BOMBADD` | Bombadd packet. |
| 5 | `PLI_BOMBDEL` | Bombdel packet. |
| 6 | `PLI_TOALL` | Toall packet. |
| 7 | `PLI_HORSEADD` | Horseadd packet. |
| 8 | `PLI_HORSEDEL` | Horsedel packet. |
| 9 | `PLI_ARROWADD` | Arrowadd packet. |
| 10 | `PLI_FIRESPY` | Firespy packet. |
| 11 | `PLI_THROWCARRIED` | Throwcarried packet. |
| 12 | `PLI_ITEMADD` | Itemadd packet. |
| 13 | `PLI_ITEMDEL` | Itemdel packet. |
| 14 | `PLI_CLAIMPKER` | Claimpker packet. |
| 15 | `PLI_BADDYPROPS` | Baddyprops packet. |
| 16 | `PLI_BADDYHURT` | Baddyhurt packet. |
| 17 | `PLI_BADDYADD` | Baddyadd packet. |
| 18 | `PLI_FLAGSET` | Flagset packet. |
| 19 | `PLI_FLAGDEL` | Flagdel packet. |
| 20 | `PLI_OPENCHEST` | Openchest packet. |
| 21 | `PLI_PUTNPC` | Putnpc packet. |
| 22 | `PLI_NPCDEL` | Npcdel packet. |
| 23 | `PLI_WANTFILE` | Wantfile packet. |
| 24 | `PLI_SHOWIMG` | Showimg packet. |
| 25 | `PLI_UNKNOWN25` | Reserved or unknown packet slot. |
| 26 | `PLI_HURTPLAYER` | Hurtplayer packet. |
| 27 | `PLI_EXPLOSION` | Explosion packet. |
| 28 | `PLI_PRIVATEMESSAGE` | Privatemessage packet. |
| 29 | `PLI_NPCWEAPONDEL` | Npcweapondel packet. |
| 30 | `PLI_LEVELWARPMOD` | Levelwarpmod packet. |
| 31 | `PLI_PACKETCOUNT` | Packetcount packet. |
| 32 | `PLI_ITEMTAKE` | Itemtake packet. |
| 33 | `PLI_WEAPONADD` | Weaponadd packet. |
| 34 | `PLI_UPDATEFILE` | Updatefile packet. |
| 35 | `PLI_ADJACENTLEVEL` | Adjacentlevel packet. |
| 36 | `PLI_HITOBJECTS` | Hitobjects packet. |
| 37 | `PLI_LANGUAGE` | Language packet. |
| 38 | `PLI_TRIGGERACTION` | Triggeraction packet. |
| 39 | `PLI_MAPINFO` | Mapinfo packet. |
| 40 | `PLI_SHOOT` | Shoot packet. |
| 41 | `PLI_SERVERWARP` | Serverwarp packet. |
| 43 | `PLI_MUTEPLAYER` | Triggered via playerlist -> mute, looks like {GSHORT playerId}{GBYTE 1/0} |
| 44 | `PLI_PROCESSLIST` | Processlist packet. |
| 46 | `PLI_UNKNOWN46` | Always is 1. Might be a player count for the gmap level. |
| 47 | `PLI_VERIFYWANTSEND` | {47}{GINT5:CRC32 Checksum]{GSTRING: Filename} - Check if the file is out of date, and if so send it |
| 48 | `PLI_SHOOT2` | Shoot2 packet. |
| 50 | `PLI_RAWDATA` | Rawdata packet. |
| 51 | `PLI_RC_SERVEROPTIONSGET` | Rc Serveroptionsget packet. |
| 52 | `PLI_RC_SERVEROPTIONSSET` | Rc Serveroptionsset packet. |
| 53 | `PLI_RC_FOLDERCONFIGGET` | Rc Folderconfigget packet. |
| 54 | `PLI_RC_FOLDERCONFIGSET` | Rc Folderconfigset packet. |
| 55 | `PLI_RC_RESPAWNSET` | Rc Respawnset packet. |
| 56 | `PLI_RC_HORSELIFESET` | Rc Horselifeset packet. |
| 57 | `PLI_RC_APINCREMENTSET` | Rc Apincrementset packet. |
| 58 | `PLI_RC_BADDYRESPAWNSET` | Rc Baddyrespawnset packet. |
| 59 | `PLI_RC_PLAYERPROPSGET` | Rc Playerpropsget packet. |
| 60 | `PLI_RC_PLAYERPROPSSET` | Rc Playerpropsset packet. |
| 61 | `PLI_RC_DISCONNECTPLAYER` | Rc Disconnectplayer packet. |
| 62 | `PLI_RC_UPDATELEVELS` | Rc Updatelevels packet. |
| 63 | `PLI_RC_ADMINMESSAGE` | Rc Adminmessage packet. |
| 64 | `PLI_RC_PRIVADMINMESSAGE` | Rc Privadminmessage packet. |
| 65 | `PLI_RC_LISTRCS` | Rc Listrcs packet. |
| 66 | `PLI_RC_DISCONNECTRC` | Rc Disconnectrc packet. |
| 67 | `PLI_RC_APPLYREASON` | Rc Applyreason packet. |
| 68 | `PLI_RC_SERVERFLAGSGET` | Rc Serverflagsget packet. |
| 69 | `PLI_RC_SERVERFLAGSSET` | Rc Serverflagsset packet. |
| 70 | `PLI_RC_ACCOUNTADD` | Rc Accountadd packet. |
| 71 | `PLI_RC_ACCOUNTDEL` | Rc Accountdel packet. |
| 72 | `PLI_RC_ACCOUNTLISTGET` | Rc Accountlistget packet. |
| 73 | `PLI_RC_PLAYERPROPSGET2` | Gets by player ID |
| 74 | `PLI_RC_PLAYERPROPSGET3` | Gets by player account name. |
| 75 | `PLI_RC_PLAYERPROPSRESET` | Rc Playerpropsreset packet. |
| 76 | `PLI_RC_PLAYERPROPSSET2` | Rc Playerpropsset2 packet. |
| 77 | `PLI_RC_ACCOUNTGET` | Rc Accountget packet. |
| 78 | `PLI_RC_ACCOUNTSET` | Rc Accountset packet. |
| 79 | `PLI_RC_CHAT` | Rc Chat packet. |
| 80 | `PLI_PROFILEGET` | Profileget packet. |
| 81 | `PLI_PROFILESET` | Profileset packet. |
| 82 | `PLI_RC_WARPPLAYER` | Rc Warpplayer packet. |
| 83 | `PLI_RC_PLAYERRIGHTSGET` | Rc Playerrightsget packet. |
| 84 | `PLI_RC_PLAYERRIGHTSSET` | Rc Playerrightsset packet. |
| 85 | `PLI_RC_PLAYERCOMMENTSGET` | Rc Playercommentsget packet. |
| 86 | `PLI_RC_PLAYERCOMMENTSSET` | Rc Playercommentsset packet. |
| 87 | `PLI_RC_PLAYERBANGET` | Rc Playerbanget packet. |
| 88 | `PLI_RC_PLAYERBANSET` | Rc Playerbanset packet. |
| 89 | `PLI_RC_FILEBROWSER_START` | Rc Filebrowser Start packet. |
| 90 | `PLI_RC_FILEBROWSER_CD` | Rc Filebrowser Cd packet. |
| 91 | `PLI_RC_FILEBROWSER_END` | Rc Filebrowser End packet. |
| 92 | `PLI_RC_FILEBROWSER_DOWN` | Rc Filebrowser Down packet. |
| 93 | `PLI_RC_FILEBROWSER_UP` | Rc Filebrowser Up packet. |
| 94 | `PLI_NPCSERVERQUERY` | Npcserverquery packet. |
| 96 | `PLI_RC_FILEBROWSER_MOVE` | Rc Filebrowser Move packet. |
| 97 | `PLI_RC_FILEBROWSER_DELETE` | Rc Filebrowser Delete packet. |
| 98 | `PLI_RC_FILEBROWSER_RENAME` | Rc Filebrowser Rename packet. |
| 100 | `PLI_NC_LISTNPCS` | {100} Request all database NPCs |
| 103 | `PLI_NC_NPCGET` | {103}{INT id} |
| 104 | `PLI_NC_NPCDELETE` | {104}{INT id} |
| 105 | `PLI_NC_NPCRESET` | {105}{INT id} |
| 106 | `PLI_NC_NPCSCRIPTGET` | {106}{INT id} |
| 107 | `PLI_NC_NPCWARP` | {107}{INT id}{CHAR x*2}{CHAR y*2}{level} |
| 108 | `PLI_NC_NPCFLAGSGET` | {108}{INT id} |
| 109 | `PLI_NC_NPCSCRIPTSET` | {109}{INT id}{GSTRING script} |
| 110 | `PLI_NC_NPCFLAGSSET` | {110}{INT id}{GSTRING flags} |
| 111 | `PLI_NC_NPCADD` | {111}{GSTRING info} - (info) name,id,type,scripter,starting level,x,y |
| 112 | `PLI_NC_CLASSEDIT` | {112}{class} |
| 113 | `PLI_NC_CLASSADD` | {113}{CHAR name length}{name}{GSTRING script} |
| 114 | `PLI_NC_LOCALNPCSGET` | {114}{level} |
| 115 | `PLI_NC_WEAPONLISTGET` | {115} |
| 116 | `PLI_NC_WEAPONGET` | {116}{weapon} |
| 117 | `PLI_NC_WEAPONADD` | {117}{CHAR weapon length}{weapon}{CHAR image length}{image}{code} |
| 118 | `PLI_NC_WEAPONDELETE` | {118}{weapon} |
| 119 | `PLI_NC_CLASSDELETE` | {119}{class} |
| 130 | `PLI_REQUESTUPDATEBOARD` | {130}{CHAR level length}{level}{INT5 modtime}{SHORT x}{SHORT y}{SHORT width}{SHORT height} |
| 150 | `PLI_NC_LEVELLISTGET` | {150} |
| 151 | `PLI_NC_LEVELLISTSET` | {151}{GSTRING levels} |
| 152 | `PLI_REQUESTTEXT` | Gets a value from the server. |
| 154 | `PLI_SENDTEXT` | Sets a value on the server. |
| 155 | `PLI_RC_LARGEFILESTART` | Rc Largefilestart packet. |
| 156 | `PLI_RC_LARGEFILEEND` | Rc Largefileend packet. |
| 157 | `PLI_UPDATEGANI` | GANI-related payload; subformat not established. |
| 158 | `PLI_UPDATESCRIPT` | {158}{script} Requests a script from the server. |
| 159 | `PLI_UPDATEPACKAGEREQUESTFILE` | {159]{(char)order?]{packagefilename}{modtime} |
| 160 | `PLI_RC_FOLDERDELETE` | Rc Folderdelete packet. |
| 161 | `PLI_UPDATECLASS` | {161}{INT5 modtime}{name} Class request. |
| 162 | `PLI_RC_UNKNOWN162` | Blank packet, sent by RC3 beta. |

## Game Server to Client (PLO)

| ID | Name | Wire note |
| ---: | --- | --- |
| 0 | `PLO_LEVELBOARD` | Levelboard packet. |
| 1 | `PLO_LEVELLINK` | Levellink packet. |
| 2 | `PLO_BADDYPROPS` | Baddyprops packet. |
| 3 | `PLO_NPCPROPS` | Npcprops packet. |
| 4 | `PLO_LEVELCHEST` | Levelchest packet. |
| 5 | `PLO_LEVELSIGN` | Levelsign packet. |
| 6 | `PLO_LEVELNAME` | Levelname packet. |
| 7 | `PLO_BOARDMODIFY` | Boardmodify packet. |
| 8 | `PLO_OTHERPLPROPS` | Otherplprops packet. |
| 9 | `PLO_PLAYERPROPS` | Playerprops packet. |
| 10 | `PLO_ISLEADER` | Isleader packet. |
| 11 | `PLO_BOMBADD` | Bombadd packet. |
| 12 | `PLO_BOMBDEL` | Bombdel packet. |
| 13 | `PLO_TOALL` | Toall packet. |
| 14 | `PLO_PLAYERWARP` | Playerwarp packet. |
| 15 | `PLO_WARPFAILED` | Warpfailed packet. |
| 16 | `PLO_DISCMESSAGE` | Discmessage packet. |
| 17 | `PLO_HORSEADD` | Horseadd packet. |
| 18 | `PLO_HORSEDEL` | Horsedel packet. |
| 19 | `PLO_ARROWADD` | Arrowadd packet. |
| 20 | `PLO_FIRESPY` | Firespy packet. |
| 21 | `PLO_THROWCARRIED` | Throwcarried packet. |
| 22 | `PLO_ITEMADD` | Itemadd packet. |
| 23 | `PLO_ITEMDEL` | Itemdel packet. |
| 24 | `PLO_NPCMOVED` | Observed packet slot; payload subformat not established. |
| 25 | `PLO_SIGNATURE` | Signature packet. |
| 26 | `PLO_NPCACTION` | Observed packet slot; payload subformat not established. |
| 27 | `PLO_BADDYHURT` | Baddyhurt packet. |
| 28 | `PLO_FLAGSET` | Flagset packet. |
| 29 | `PLO_NPCDEL` | Npcdel packet. |
| 30 | `PLO_FILESENDFAILED` | Filesendfailed packet. |
| 31 | `PLO_FLAGDEL` | Flagdel packet. |
| 32 | `PLO_SHOWIMG` | Showimg packet. |
| 33 | `PLO_NPCWEAPONADD` | Npcweaponadd packet. |
| 34 | `PLO_NPCWEAPONDEL` | Npcweapondel packet. |
| 35 | `PLO_RC_ADMINMESSAGE` | Rc Adminmessage packet. |
| 36 | `PLO_EXPLOSION` | Explosion packet. |
| 37 | `PLO_PRIVATEMESSAGE` | Privatemessage packet. |
| 38 | `PLO_PUSHAWAY` | Observed packet slot; payload subformat not established. |
| 39 | `PLO_LEVELMODTIME` | Levelmodtime packet. |
| 40 | `PLO_HURTPLAYER` | Hurtplayer packet. |
| 41 | `PLO_STARTMESSAGE` | 037. |
| 42 | `PLO_NEWWORLDTIME` | Newworldtime packet. |
| 43 | `PLO_DEFAULTWEAPON` | Defaultweapon packet. |
| 44 | `PLO_HASNPCSERVER` | If sent, the client won't update npc props. 07+. |
| 45 | `PLO_FILEUPTODATE` | Fileuptodate packet. |
| 46 | `PLO_HITOBJECTS` | Hitobjects packet. |
| 47 | `PLO_STAFFGUILDS` | Staffguilds packet. |
| 48 | `PLO_TRIGGERACTION` | Triggeraction packet. |
| 49 | `PLO_PLAYERWARP2` | Bytes 1-3 are x/y/z. 4 = level x in gmap, 5 = level y in gmap. |
| 50 | `PLO_RC_ACCOUNTADD` | Deprecated. 037. |
| 51 | `PLO_RC_ACCOUNTSTATUS` | Deprecated. 037. |
| 52 | `PLO_RC_ACCOUNTNAME` | Deprecated. 037. |
| 53 | `PLO_RC_ACCOUNTDEL` | Deprecated. 037. |
| 54 | `PLO_RC_ACCOUNTPROPS` | Deprecated. 037. |
| 55 | `PLO_ADDPLAYER` | 07+. |
| 56 | `PLO_DELPLAYER` | 07+. |
| 57 | `PLO_RC_ACCOUNTPROPSGET` | Deprecated. 037. |
| 58 | `PLO_RC_ACCOUNTCHANGE` | Deprecated. 037. |
| 59 | `PLO_RC_PLAYERPROPSCHANGE` | Deprecated. 037. |
| 60 | `PLO_UNKNOWN60` | 07+. |
| 61 | `PLO_RC_SERVERFLAGSGET` | 07+. |
| 62 | `PLO_RC_PLAYERRIGHTSGET` | 07+. |
| 63 | `PLO_RC_PLAYERCOMMENTSGET` | 07+. |
| 64 | `PLO_RC_PLAYERBANGET` | 07+. |
| 65 | `PLO_RC_FILEBROWSER_DIRLIST` | 07+. |
| 66 | `PLO_RC_FILEBROWSER_DIR` | 07+. |
| 67 | `PLO_RC_FILEBROWSER_MESSAGE` | Rc Filebrowser Message packet. |
| 68 | `PLO_LARGEFILESTART` | Largefilestart packet. |
| 69 | `PLO_LARGEFILEEND` | Largefileend packet. |
| 70 | `PLO_RC_ACCOUNTLISTGET` | 07+. |
| 71 | `PLO_RC_PLAYERPROPS` | Deprecated. 037. |
| 72 | `PLO_RC_PLAYERPROPSGET` | 07+. |
| 73 | `PLO_RC_ACCOUNTGET` | 07+. |
| 74 | `PLO_RC_CHAT` | Rc Chat packet. |
| 75 | `PLO_PROFILE` | 037. |
| 76 | `PLO_RC_SERVEROPTIONSGET` | 07+. |
| 77 | `PLO_RC_FOLDERCONFIGGET` | 07+. |
| 78 | `PLO_NC_CONTROL` | Implementation-specific control slot. |
| 79 | `PLO_NPCSERVERADDR` | Bytes 1-2 are 0 and 2, followed by a string formatted as <ipaddr>,<port>. 07+. |
| 80 | `PLO_NC_LEVELLIST` | {80}{GSTRING levels} 07+. |
| 81 | `PLO_UNKNOWN81` | 07+. |
| 82 | `PLO_SERVERTEXT` | Answer to PLI_REQUESTTEXT and PLI_SENDTEXT. |
| 83 | `PLO_UNKNOWN83` | 07+. |
| 84 | `PLO_LARGEFILESIZE` | Largefilesize packet. |
| 100 | `PLO_RAWDATA` | {100}{INT3 length} 07+. |
| 101 | `PLO_BOARDPACKET` | Boardpacket packet. |
| 102 | `PLO_FILE` | File packet. |
| 103 | `PLO_RC_MAXUPLOADFILESIZE` | {GINT5} - Upload file size limit in bytes. Default to 20 mebibytes |
| 104 | `PLO_UNKNOWN104` | 07 and 6.037. |
| 105 | `PLO_UPDATEPACKAGESIZE` | 07 and 6.037. |
| 106 | `PLO_UPDATEPACKAGEDONE` | 07 and 6.037. |
| 107 | `PLO_BOARDLAYER` | Basically PLO_LEVELBOARD for extra level layers. |
| 109 | `PLO_UNKNOWN109` | 037. |
| 111 | `PLO_UNKNOWN111` | 037. |
| 124 | `PLO_UNKNOWN124` | Observed packet slot; payload subformat not established. |
| 131 | `PLO_NPCBYTECODE` | Compiled Torque-script for an NPC. {131}{INT3 id}{code} |
| 132 | `PLO_UNKNOWN132` | 07. 037. |
| 133 | `PLO_UNKNOWN133` | 07. 037. |
| 134 | `PLO_GANISCRIPT` | Might be used for package downloads. Looks to send gani scripts to client |
| 140 | `PLO_NPCWEAPONSCRIPT` | {140}{INT2 info_length}{script} |
| 150 | `PLO_NPCDEL2` | {150}{CHAR level_length}{level}{INT3 npcid} |
| 151 | `PLO_HIDENPCS` | Hidenpcs packet. |
| 153 | `PLO_SAY2` | Also used for signs. {153}{text} |
| 154 | `PLO_FREEZEPLAYER2` | Blank. |
| 155 | `PLO_UNFREEZEPLAYER` | Blank. |
| 156 | `PLO_SETACTIVELEVEL` | Sets the level to receive chests, baddies, NPCs, etc. |
| 157 | `PLO_NC_NPCATTRIBUTES` | {157}{GSTRING attributes} 07+. |
| 158 | `PLO_NC_NPCADD` | {158}{INT id}{CHAR 50}{CHAR name length}{name}{CHAR 51}{CHAR type length}{type}{CHAR 52}{CHAR level length}{level} 07+. |
| 159 | `PLO_NC_NPCDELETE` | {159}{INT id} 07+. |
| 160 | `PLO_NC_NPCSCRIPT` | {160}{INT id}{GSTRING script} 07+. |
| 161 | `PLO_NC_NPCFLAGS` | {161}{INT id}{GSTRING flags} 07+. |
| 162 | `PLO_NC_CLASSGET` | {162}{CHAR name length}{name}{GSTRING script} 07+. |
| 163 | `PLO_NC_CLASSADD` | {163}{class} 07+. |
| 164 | `PLO_NC_LEVELDUMP` | 07+. |
| 165 | `PLO_MOVE` | 037. |
| 166 | `PLO_UNKNOWN166` | 07 and 6.037. |
| 167 | `PLO_NC_WEAPONLISTGET` | {167}{CHAR name1 length}{name1}{CHAR name2 length}{name2}.. 07+. |
| 168 | `PLO_UNKNOWN168` | Login server sends this. Blank packet. |
| 169 | `PLO_UNKNOWN169` | Observed packet slot; payload subformat not established. |
| 170 | `PLO_GHOSTMODE` | Ghostmode packet. |
| 171 | `PLO_BIGMAP` | 037. |
| 172 | `PLO_MINIMAP` | Map/minimap resource payload; subformat not established. |
| 173 | `PLO_GHOSTTEXT` | {173}{text} Shows static text in lower-right corner of screen only when in ghost mode. |
| 174 | `PLO_GHOSTICON` | Pass 1 to enable the ghost icon |
| 175 | `PLO_SHOOT` | 037. |
| 176 | `PLO_FULLSTOP` | Sending this causes the entire client to not respond to normal input and it hides the HUD. |
| 177 | `PLO_FULLSTOP2` | Sending this causes the entire client to not respond to normal input and it hides the HUD. 07+. |
| 178 | `PLO_SERVERWARP` | Serverwarp packet. |
| 179 | `PLO_RPGWINDOW` | Rpgwindow packet. |
| 180 | `PLO_STATUSLIST` | Statuslist packet. |
| 181 | `PLO_UNKNOWN181` | 07. 037. |
| 182 | `PLO_LISTPROCESSES` | Listprocesses packet. |
| 183 | `PLO_UNKNOWN183` | 07 and 6.037. |
| 184 | `PLO_UNKNOWN184` | Implementation-specific payload; subformat not established. |
| 185 | `PLO_UNKNOWN185` | 07 and 6.037. |
| 186 | `PLO_UNKNOWN186` | 07 and 6.037. |
| 187 | `PLO_UPDATEPACKAGEISUPDATED` | 07 and 6.037. |
| 188 | `PLO_NC_CLASSDELETE` | {188}{class} 07+. |
| 189 | `PLO_MOVE2` | {189}{INT id}.. |
| 190 | `PLO_UNKNOWN190` | Auxiliary service notification; payload may be empty. |
| 191 | `PLO_SHOOT2` | 07 and 6.037. |
| 192 | `PLO_NC_WEAPONGET` | {192}{CHAR name length}{name}{CHAR image length}{image}{script} 07+. |
| 193 | `PLO_UNKNOWN193` | Observed packet slot; payload subformat not established. |
| 194 | `PLO_CLEARWEAPONS` | Auxiliary service notification; payload may be empty. |
| 195 | `PLO_UNKNOWN195` | GANI-related payload; subformat not established. |
| 197 | `PLO_UNKNOWN197` | Observed packet slot; payload subformat not established. |
| 198 | `PLO_UNKNOWN198` | 037. |

## Client to List Server (SVO)

| ID | Name | Wire note |
| ---: | --- | --- |
| 0 | `SVO_SETNAME` | Setname packet. |
| 1 | `SVO_SETDESC` | Setdesc packet. |
| 2 | `SVO_SETLANG` | Setlang packet. |
| 3 | `SVO_SETVERS` | Setvers packet. |
| 4 | `SVO_SETURL` | Seturl packet. |
| 5 | `SVO_SETIP` | Setip packet. |
| 6 | `SVO_SETPORT` | Setport packet. |
| 7 | `SVO_SETPLYR` | Setplyr packet. |
| 8 | `SVO_VERIACC` | Deprecated packet slot. |
| 9 | `SVO_VERIGUILD` | Veriguild packet. |
| 10 | `SVO_GETFILE` | Deprecated packet slot. |
| 11 | `SVO_NICKNAME` | Nickname packet. |
| 12 | `SVO_GETPROF` | Getprof packet. |
| 13 | `SVO_SETPROF` | Setprof packet. |
| 14 | `SVO_PLYRADD` | Plyradd packet. |
| 15 | `SVO_PLYRREM` | Plyrrem packet. |
| 16 | `SVO_PING` | Ping packet. |
| 17 | `SVO_VERIACC2` | Veriacc2 packet. |
| 18 | `SVO_SETLOCALIP` | Setlocalip packet. |
| 19 | `SVO_GETFILE2` | Deprecated packet slot. |
| 20 | `SVO_UPDATEFILE` | Updatefile packet. |
| 21 | `SVO_GETFILE3` | Getfile3 packet. |
| 22 | `SVO_NEWSERVER` | Newserver packet. |
| 23 | `SVO_SERVERHQPASS` | Serverhqpass packet. |
| 24 | `SVO_SERVERHQLEVEL` | Serverhqlevel packet. |
| 25 | `SVO_SERVERINFO` | Serverinfo packet. |
| 26 | `SVO_REQUESTLIST` | Requestlist packet. |
| 27 | `SVO_REQUESTSVRINFO` | Requestsvrinfo packet. |
| 28 | `SVO_REQUESTBUDDIES` | Requestbuddies packet. |
| 29 | `SVO_PMPLAYER` | Pmplayer packet. |
| 30 | `SVO_REGISTERV3` | Registerv3 packet. |
| 31 | `SVO_SENDTEXT` | Sendtext packet. |

## List Server to Client (SVI)

| ID | Name | Wire note |
| ---: | --- | --- |
| 0 | `SVI_VERIACC` | Deprecated packet slot. |
| 1 | `SVI_VERIGUILD` | Veriguild packet. |
| 2 | `SVI_FILESTART` | Deprecated packet slot. |
| 3 | `SVI_FILEEND` | Deprecated packet slot. |
| 4 | `SVI_FILEDATA` | Deprecated packet slot. |
| 5 | `SVI_VERSIONOLD` | Versionold packet. |
| 6 | `SVI_VERSIONCURRENT` | Versioncurrent packet. |
| 7 | `SVI_PROFILE` | Profile packet. |
| 8 | `SVI_ERRMSG` | Errmsg packet. |
| 9 | `SVI_NULL4` | Null4 packet. |
| 10 | `SVI_NULL5` | Null5 packet. |
| 11 | `SVI_VERIACC2` | Veriacc2 packet. |
| 12 | `SVI_FILESTART2` | Deprecated packet slot. |
| 13 | `SVI_FILEDATA2` | Deprecated packet slot. |
| 14 | `SVI_FILEEND2` | Deprecated packet slot. |
| 15 | `SVI_FILESTART3` | Filestart3 packet. |
| 16 | `SVI_FILEDATA3` | Filedata3 packet. |
| 17 | `SVI_FILEEND3` | Fileend3 packet. |
| 18 | `SVI_SERVERINFO` | Serverinfo packet. |
| 19 | `SVI_REQUESTTEXT` | Requesttext packet. |
| 20 | `SVI_SENDTEXT` | Sendtext packet. |
| 29 | `SVI_PMPLAYER` | Pmplayer packet. |
| 30 | `SVI_ASSIGNPCID` | Assignpcid packet. |
| 99 | `SVI_PING` | Ping packet. |
| 100 | `SVI_RAWDATA` | Rawdata packet. |

## Interpretation rules

- An `UNKNOWN` slot is still a valid numeric packet value; unknown means that this guide does not assign semantics to it.
- `PLI` and `PLO` values describe the ordinary game-client direction.
- `SVO` and `SVI` values describe auxiliary list/server exchanges, not the ordinary game socket payload.
- Handler-table indices, script callback indices, and wire packet IDs are different namespaces.
- Preserve the raw payload for every packet, including packets whose name is known but whose fields are incomplete.
