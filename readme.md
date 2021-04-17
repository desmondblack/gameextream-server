This is my SA:MP server project writed on PAWN. Project writen from 0. Now him estimated 89k lines. 
Examples of the code:

Command used to warn player. If him to have 3 warns, player get a ban for 3 days.

```C	if(strcmp(cmd, "/warn", true) == 0)
	{
		if(PlayerInfo[playerid][pAdmin] < 1) return true;
		if(PlayerInfo[playerid][pAdmin] < 5) return SendFormatMessage(playerid, COLOR_GREY, "{F70000}x {AFAFAF}Ваш уровень администратора: %d. Команда доступна с 5!", PlayerInfo[playerid][pAdmin]);
		tmp = strtok(cmdtext, idx);
		if(!strlen(tmp)) return SendClientMessage(playerid, COLOR_GRAD2, "ИСПОЛЬЗОВАНИЕ: /warn {FFFFFF}[id игрока/ник игрока] {BFC0C2}[причина]");
		para1 = ReturnUser(tmp);
		if(!IsPlayerConnected(para1) || para1 == INVALID_PLAYER_ID) return SendClientMessage(playerid, COLOR_GREY, "{F70000}x {AFAFAF}Игрок не в сети!");
		if(logged[para1] < 1) return SendClientMessage(playerid, COLOR_GREY, "{F70000}x {AFAFAF}Игрок не авторизировался!");
		if(playerid == para1) return SendClientMessage(playerid, COLOR_GREY, "{F70000}x {AFAFAF}Вы не можете сделать себе предупреждение!");
		if(PlayerInfo[para1][pAdmin] > 0 && AdminInkognito[para1] == false) return SendClientMessage(playerid, COLOR_GREY, "{F70000}x {AFAFAF}Нельзя сделать предупреждение Администратору!");
		result = sresult(cmdtext, idx);
		if(!strlen(result)) return SendClientMessage(playerid, COLOR_GRAD2, "ИСПОЛЬЗОВАНИЕ: /warn [id игрока/ник игрока] {FFFFFF}[причина]");
		PlayerWarn(para1,PlayerInfo[playerid][pNick],result);
		return true;
	}
```
Stock process the command:
```
  stock PlayerWarn(playerid,admin[],result[],text[]=0)
{
	new string[128];
    if(strcmp(admin,"Anti-Cheat", true) == 0)
    {
	    format(string, sizeof(string), "Игрок %s[ID:%d] получил предупреждение от сервера, причина: %s (пометка: %s)", PlayerInfo[playerid][pNick], PlayerInfo[playerid][pID], result, text);
		Log("JailWarns", string);
		format(string, sizeof(string), "%s получает предупреждение от сервера, причина: %s", PlayerName[playerid], result);
		SendClientMessageToAll(COLOR_LIGHTRED, string);
		SendClientMessage(playerid,COLOR_LIGHTRED2,"* Вы получили предупреждение от сервера! Вы посажены в камеру на 60 минут!");
	}
	else
	{
	    format(string, sizeof(string), "Игрок %s[ID:%d] получил предупреждение от администратора %s, причина: %s", PlayerInfo[playerid][pNick], PlayerInfo[playerid][pID], admin, result);
		Log("JailWarns", string);
		format(string, sizeof(string), "%s получает предупреждение от администратора %s, причина: %s", PlayerName[playerid], admin, result);
		SendClientMessageToAll(COLOR_LIGHTRED, string);
		SendClientMessage(playerid,COLOR_LIGHTRED2,"* Вы получили предупреждение от администрации! Вы посажены в камеру на 60 минут!");
	}
	PlayerInfo[playerid][pWarns]++, SaveAccount(playerid,spWarns,1);
	if(PlayerInfo[playerid][pWarns] < 3)
	{
		SendClientMessage(playerid,COLOR_LIGHTRED2,"* Если у вас будет 3 предупреждения вы будете автоматически забанены на 3 дня!");
	}
 	DeathOfaCriminal(playerid,INVALID_PLAYER_ID,"посадку в камеру","был посажен в камеру",false,false,true,60,20);
	if(PlayerInfo[playerid][pWarns] > 2)
	{
	    format(string, sizeof(string), "Игрок %s[ID:%d] забанен сервером на 3 дня, причина: 3 предупреждения.", PlayerInfo[playerid][pNick], PlayerInfo[playerid][pID]);
		Log("KickBans", string);
		PlayerInfo[playerid][pWarns] = 0, SaveAccount(playerid,spWarns);
		AddBan(playerid, "Anti-Cheat", 0, 3, "3 предупреждения", "");
	}
	return true;
}
```
If player has 3 warns:
```
stock AddBan(playerid, zabanil[], skill, days, reason[], result[])
{
	if(logged[playerid] == -6) return true;
	new string[256];
	switch(skill)
	{
	    case 0: level = days;
	    case 1:
	    {
			switch(PlayerInfo[playerid][pBanSkill])
			{
				case 0:  level = 1;
				case 1:  level = 3;
				case 2:  level = 7;
				case 3:  level = 14;
				case 4:  level = 30;
				case 5:  level = 45;
				default: level = 60;
			}
			if(PlayerInfo[playerid][pAdmin] < 1 && PlayerInfo[playerid][pBanSkill] < 6)
			{
				PlayerInfo[playerid][pBanSkill] ++, SaveAccount(playerid,spBanSkill);
			}
		}
	}
	if(PlayerInfo[playerid][pAdmin] > 0)
	{
	    format(string, sizeof(string), "[Anti-cheat system]: Администратор %s[%d] был кикнут сервером, причина: %s", PlayerName[playerid], playerid, reason);
		ABroadCast(COLOR_LIGHTRED2,string,1);
		format(string, sizeof(string), "[Anti-cheat system]: Result: %s", result);
		ABroadCast(COLOR_LIGHTRED2,string,1);
		format(string, sizeof(string), "Администратор %s[ID:%d] бан на %d %s, причина: %s, примечание: %s", PlayerInfo[playerid][pNick], PlayerInfo[playerid][pID], level, TimeDays(level), reason, result);
		Log("AdminKickBans", string);
		KickAC(playerid);
	    return true;
	}
	if(strcmp(zabanil,"Anti-Cheat", true) == 0)
	{
	    if(strcmp(reason,"3 предупреждения", true) == 0)
	    {
	    	SendClientMessage(playerid,COLOR_YELLOW,"Вы были забанены сервером на 3 дня, причина: Получили 3 предупреждения(warn's)");
		}
		else
		{
			SendClientMessage(playerid, COLOR_YELLOW,".::GameExtream[RP]::. : Вы были забанены античитом сервера!");
			SendClientMessage(playerid, COLOR_YELLOW,".::GameExtream[RP]::. : Вы можете обратиться к администрации сервера по поводу вашего бана!");
			SendClientMessage(playerid, COLOR_YELLOW,".::GameExtream[RP]::. : Внимание! Сделайте скрин экрана клавишей F8!!");
		}
		format(string, sizeof(string), "Игрок %s[ID:%d] забанен сервером на %d %s, причина: %s, примечание: %s", PlayerInfo[playerid][pNick], PlayerInfo[playerid][pID], level, TimeDays(level), reason, result);
		Log("KickBans", string);
		format(string, sizeof(string), ".::GameExtream[RP]::. : %s был забанен сервером на %d %s, причина: %s", PlayerInfo[playerid][pNick], level, TimeDays(level), reason);
		SendClientMessageToAll(COLOR_LIGHTRED2, string);
	}
	else
	{
		SendClientMessage(playerid, COLOR_YELLOW,".::GameExtream[RP]::. : Вы были забанены администратором сервера!");
		SendClientMessage(playerid, COLOR_YELLOW,".::GameExtream[RP]::. : Вы можете обратиться к администрации сервера по поводу вашего бана!");
		SendClientMessage(playerid, COLOR_YELLOW,".::GameExtream[RP]::. : Внимание! Сделайте скрин экрана клавишей F8!!");
        format(string, sizeof(string), "Игрок %s[ID:%d] забанен сервером на %d %s, причина: %s, примечание: %s", PlayerInfo[playerid][pNick], PlayerInfo[playerid][pID], level, TimeDays(level), reason, result);
		Log("KickBans", string);
		format(string, sizeof(string), ".::GameExtream[RP]::. : %s был забанен администратором %s на %d %s, причина: %s", PlayerInfo[playerid][pNick], zabanil, level, TimeDays(level), reason);
		if(skill == 0)
		{
			SendClientMessageToAll(COLOR_LIGHTRED2, string);
		}
		if(skill == 1)
		{
			SendClientMessageToAll(COLOR_LIGHTRED, string);
		}
	}
	mysql_format(base, asd, sizeof(asd), "INSERT INTO `banlist` (`ID`, `Nick`, `IP`, `Bantime`, `Unbantime`, `Zabanil`, `Reason`, `Days`, `Result`) VALUES ('%d', '%e', '%e', '%d', '%d', '%e', '%e', '%d', '%s')", PlayerInfo[playerid][pID], PlayerInfo[playerid][pNick], PlayerInfo[playerid][pIP], gettime(), gettime()+level*86400, zabanil, reason, level, result);
	mysql_tquery(base, asd, "", "");
	mysql_format(base, asd, sizeof(asd), "INSERT INTO `bannedip` (`IP`, `Zabanil`, `Days`, `Reason`, `Bantime`, `Unbantime`) VALUES ('%e', '%e', '%d', '%e', '%d', '%d')", PlayerInfo[playerid][pIP], zabanil, level, reason, gettime(), gettime()+level*86400);
	mysql_tquery(base, asd, "", "");
	mysql_format(base, asd, sizeof(asd), "INSERT INTO `banlisted` (`ID`, `Nick`, `IP`, `Bantime`, `Days`, `Zabanil`, `Reason`) VALUES ('%d', '%e', '%e', '%d', '%d', '%e', '%e')", PlayerInfo[playerid][pID], PlayerInfo[playerid][pNick], PlayerInfo[playerid][pIP], gettime(), level, zabanil, reason);
	mysql_tquery(base, asd, "", "");
	format(string, sizeof(string), "Игрок %s[ID:%d] был кикнут сервером, причина: Забанили.", PlayerInfo[playerid][pNick], PlayerInfo[playerid][pID]);
	Log("KickBans", string);
	KickAC(playerid);
	return true;
}
```
Admin may check banned players:
```
	if(strcmp(cmd,"/banlist",true) == 0)
	{
		if(PlayerInfo[playerid][pAdmin] < 1) return true;
		if(PlayerInfo[playerid][pAdmin] < 3) return SendFormatMessage(playerid, COLOR_GREY, "{F70000}x {AFAFAF}Ваш уровень администратора: %d. Команда доступна с 3!", PlayerInfo[playerid][pAdmin]);
		tmp = strtok(cmdtext, idx);
		if(!strlen(tmp)) return SendClientMessage(playerid, COLOR_GRAD2, "ИСПОЛЬЗОВАНИЕ: /banlist {FFFFFF}[ник игрока]");
		mysql_format(base, asd,sizeof(asd), "SELECT * FROM `banlist` WHERE `Nick` = '%e'",tmp);
		mysql_function_query(base,asd, true, "createdacc","dsd", playerid, tmp, 12);
		return true;
	}
```	
Command /banlist used public crearedacc and process mysql query:
```
	case 12: 
		{
			if(!rows) return SendFormatMessage(playerid,COLOR_GREY,"{F70000}x {AFAFAF}Аккаунт с именем %s не найден в базе данных!",params);
			if(cache_get_field_content_int(0, "Unbantime", base) <= gettime())
			{
				mysql_format(base, asd, sizeof(asd), "DELETE FROM `banlist` WHERE `Nick` = '%e'",params);
				mysql_tquery(base, asd, "", "");
				return true;
			}
			cache_get_field_content(0, "Zabanil",zabanil,base,MAX_PLAYER_NAME);
			cache_get_field_content(0, "Reason",string,base,sizeof(string));
			SendClientMessage(playerid, COLOR_LIGHTRED2, "|____________________ Информация банлиста ____________________|");
			SendFormatMessage(playerid, COLOR_LIGHTRED2, "Ник: {FFFFFF}%s[ID:%d]", params, cache_get_field_content_int(0, "ID", base));
			SendFormatMessage(playerid, COLOR_LIGHTRED2, "Забанил {FFFFFF}%s {FF0000}на {FFFFFF}%d {FF0000}дней.", zabanil, cache_get_field_content_int(0, "Days", base));
			SendFormatMessage(playerid, COLOR_LIGHTRED2, "Причина: {FFFFFF}%s", string);
			cache_get_field_content(0, "Result",string,base,sizeof(string));
			SendFormatMessage(playerid, COLOR_LIGHTRED2, "Примечание: {FFFFFF}%s", string);
			SendFormatMessage(playerid, COLOR_LIGHTRED2, "До окончании бана осталось: {FFFFFF}%s",convert(1,cache_get_field_content_int(0, "Unbantime", base)-gettime()));
			SendClientMessage(playerid, COLOR_LIGHTRED2, "|______________________________________________________________|");
		}
```		
