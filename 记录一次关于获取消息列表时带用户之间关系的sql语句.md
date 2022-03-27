### 记录一次关于获取消息列表时带用户之间关系的sql语句

```sql
#消息列表带与当前用户关系的
SELECT scl.id,scl.user_id as msgUserId,scl.friend_id as friendId,
        scl.last_message as latestMsg,scl.create_time as `timestamp`,scl.unread,
        `user`.nickname as friendName,`user`.avatar as friendHeadUrl,
        FROM_UNIXTIME(scl.create_time/1000,'%m-%d') as timeStr,
#0是都未关注，1他关注我（粉丝），2是我关注他（关注），3是互相关注
IF((select 1 from social_follow where user_id = 4 AND friend_id = scl.friend_id LIMIT 1) IS null,
IF((select 1 from social_follow where user_id = scl.friend_id AND friend_id = 4 LIMIT 1) IS NULL,0,1)
,IF((select 1 from social_follow where user_id = scl.friend_id AND friend_id = 4 LIMIT 1) IS NULL,2,3)) as follow  #主要内容是在IF语句块，friend_id = scl.friend_id
        FROM social_chat_list as scl
        LEFT JOIN `user` ON scl.friend_id = `user`.id
        WHERE scl.user_id = 4
        ORDER BY scl.create_time DESC
```

```sql
#互相关注的
SELECT `user`.id,`user`.nickname,`user`.avatar,`user`.city,
        `user`.sex,`user`.generation,`user`.constellation
        FROM `user`
WHERE id in(
SELECT DISTINCT t1.friend_id FROM (SELECT * FROM social_follow WHERE user_id = 1) AS t1 
INNER JOIN (SELECT * FROM social_follow WHERE friend_id = 1) AS t2 ON t1.friend_id = t2.user_id
order by t1.create_time desc)

#首先获取我关注的用户(SELECT * FROM social_follow WHERE user_id = 1)，再获取关注我的用户（SELECT * FROM social_follow WHERE friend_id = 1），然后使用内连接获取交集
```



