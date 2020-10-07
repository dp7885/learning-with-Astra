# learning-with-Astra

Logging in with cqlsh
User: KVUser
Password:
Connected to caas-cluster at caas-cluster-caas-dc-service:9042.
[cqlsh 6.8.0 | DSE 6.8.4.145 | CQL spec 3.4.5 | DSE protocol v2]
Use HELP for help.
KVUser@cqlsh> use killrvideo;
InvalidRequest: Error from server: code=2200 [Invalid query] message="Keyspace 'killrvideo' does not exist"
KVUser@cqlsh> desc keyspaces;

system_virtual_schema  system_views  killervideo
system_schema          system        system_traces

KVUser@cqlsh> use killervideo;
KVUser@cqlsh:killervideo> CREATE TABLE IF NOT EXISTS users_by_city (
               ... city text,
               ... last_name text,
               ... first_name text,
               ... address text,
               ... email text,
               ... PRIMARY KEY ((city), last_name, first_name, email));
KVUser@cqlsh:killervideo> desc tables;

users_by_city

KVUser@cqlsh:killervideo> CREATE TABLE IF NOT EXISTS comments_by_user (
               ...     userid uuid,
               ...     commentid timeuuid,
               ...     videoid uuid,
               ...     comment text,
               ...     PRIMARY KEY ((userid), commentid)
               ... ) WITH CLUSTERING ORDER BY (commentid DESC);
KVUser@cqlsh:killervideo> CREATE TABLE IF NOT EXISTS comments_by_video (
               ...     videoid   uuid,
               ...     commentid timeuuid,
               ...     userid    uuid,
               ...     comment   text,
               ...     PRIMARY KEY ((videoid), commentid)
               ... ) WITH CLUSTERING ORDER BY (commentid DESC);
KVUser@cqlsh:killervideo> INSERT INTO comments_by_user (
               ...   userid, //uuid: unique id for a user
               ...   commentid, //timeuuid: unique uuid + timestamp
               ...   videoid, //uuid: id for a given video
               ...   comment //text: the comment text
               ... )
               ... VALUES (
               ...   11111111-1111-1111-1111-111111111111,
               ...   NOW(),
               ...   12345678-1234-1111-1111-111111111111,
               ...   'I so grew up in the 80''s'
               ... );
KVUser@cqlsh:killervideo> select * from comments_by_user;

 userid                               | commentid                            | comment                  | videoid
--------------------------------------+--------------------------------------+--------------------------+--------------------------------------
 11111111-1111-1111-1111-111111111111 | eb0c2290-08a1-11eb-9506-dfc14281f836 | I so grew up in the 80's | 12345678-1234-1111-1111-111111111111

(1 rows)
KVUser@cqlsh:killervideo> INSERT INTO comments_by_user (userid, commentid, videoid, comment)
               ... VALUES (11111111-1111-1111-1111-111111111111, NOW(), 12345678-1234-1111-1111-111111111111, 'I keep watching this video');
KVUser@cqlsh:killervideo> INSERT INTO comments_by_user (userid, commentid, videoid, comment)
               ... VALUES (11111111-1111-1111-1111-111111111111, NOW(), 12345678-1234-1111-1111-111111111111, 'Soo many comments for the same video');
KVUser@cqlsh:killervideo> INSERT INTO comments_by_user (userid, commentid, videoid, comment)
               ... VALUES (22222222-2222-2222-2222-222222222222, NOW(), 12345678-1234-1111-1111-111111111111, 'I really like this video too!');
KVUser@cqlsh:killervideo> INSERT INTO comments_by_video (
               ...   videoid, //uuid: id for a given video
               ...   commentid, //timeuuid: unique uuid + timestamp
               ...   userid, //uuid: unique id for a user
               ...   comment //text: the comment text
               ... )
               ... VALUES (
               ...   12345678-1234-1111-1111-111111111111,
               ...   NOW(),
               ...   11111111-1111-1111-1111-111111111111,
               ...   'This is such a cool video'
               ... );
KVUser@cqlsh:killervideo> INSERT INTO comments_by_video (videoid, commentid, userid, comment)
               ... VALUES(12345678-1234-1111-1111-111111111111, NOW(), 22222222-2222-2222-2222-222222222222, 'Such a killr edit');
KVUser@cqlsh:killervideo> INSERT INTO comments_by_video (videoid, commentid, userid, comment)
               ... VALUES(12345678-1234-1111-1111-111111111111, 494a3f00-e966-11ea-84bf-83e48ffdc8ac, 77777777-7777-7777-7777-777777777777, 'OMG that guy Patrick is such a geek!');
KVUser@cqlsh:killervideo> INSERT INTO comments_by_video (videoid, commentid, userid, comment)
               ... VALUES(08765309-1234-9999-9999-111111111111, NOW(), 55555555-5555-5555-5555-555555555555, 'Never thought I''d see a music video about databases');
KVUser@cqlsh:killervideo> SELECT * FROM comments_by_user;

 userid                               | commentid                            | comment                              | videoid
--------------------------------------+--------------------------------------+--------------------------------------+--------------------------------------
 22222222-2222-2222-2222-222222222222 | 12003620-08a2-11eb-9506-dfc14281f836 |        I really like this video too! | 12345678-1234-1111-1111-111111111111
 11111111-1111-1111-1111-111111111111 | 079c9ac0-08a2-11eb-9506-dfc14281f836 | Soo many comments for the same video | 12345678-1234-1111-1111-111111111111
 11111111-1111-1111-1111-111111111111 | 06c9e170-08a2-11eb-9506-dfc14281f836 |           I keep watching this video | 12345678-1234-1111-1111-111111111111
 11111111-1111-1111-1111-111111111111 | eb0c2290-08a1-11eb-9506-dfc14281f836 |             I so grew up in the 80's | 12345678-1234-1111-1111-111111111111

(4 rows)
KVUser@cqlsh:killervideo> SELECT * FROM comments_by_user WHERE userid = 11111111-1111-1111-1111-111111111111;

 userid                               | commentid                            | comment                              | videoid
--------------------------------------+--------------------------------------+--------------------------------------+--------------------------------------
 11111111-1111-1111-1111-111111111111 | 079c9ac0-08a2-11eb-9506-dfc14281f836 | Soo many comments for the same video | 12345678-1234-1111-1111-111111111111
 11111111-1111-1111-1111-111111111111 | 06c9e170-08a2-11eb-9506-dfc14281f836 |           I keep watching this video | 12345678-1234-1111-1111-111111111111
 11111111-1111-1111-1111-111111111111 | eb0c2290-08a1-11eb-9506-dfc14281f836 |             I so grew up in the 80's | 12345678-1234-1111-1111-111111111111

(3 rows)
KVUser@cqlsh:killervideo> SELECT * FROM comments_by_user;

 userid                               | commentid                            | comment                              | videoid
--------------------------------------+--------------------------------------+--------------------------------------+--------------------------------------
 22222222-2222-2222-2222-222222222222 | 12003620-08a2-11eb-9506-dfc14281f836 |        I really like this video too! | 12345678-1234-1111-1111-111111111111
 11111111-1111-1111-1111-111111111111 | 079c9ac0-08a2-11eb-9506-dfc14281f836 | Soo many comments for the same video | 12345678-1234-1111-1111-111111111111
 11111111-1111-1111-1111-111111111111 | 06c9e170-08a2-11eb-9506-dfc14281f836 |           I keep watching this video | 12345678-1234-1111-1111-111111111111
 11111111-1111-1111-1111-111111111111 | eb0c2290-08a1-11eb-9506-dfc14281f836 |             I so grew up in the 80's | 12345678-1234-1111-1111-111111111111

(4 rows)
KVUser@cqlsh:killervideo> SELECT * FROM comments_by_video;

 videoid                              | commentid                            | comment                                             | userid
--------------------------------------+--------------------------------------+-----------------------------------------------------+--------------------------------------
 08765309-1234-9999-9999-111111111111 | 46f445b0-08a2-11eb-9506-dfc14281f836 | Never thought I'd see a music video about databases | 55555555-5555-5555-5555-555555555555
 12345678-1234-1111-1111-111111111111 | 30a0c9a0-08a2-11eb-9506-dfc14281f836 |                                   Such a killr edit | 22222222-2222-2222-2222-222222222222
 12345678-1234-1111-1111-111111111111 | 24f80410-08a2-11eb-9506-dfc14281f836 |                           This is such a cool video | 11111111-1111-1111-1111-111111111111
 12345678-1234-1111-1111-111111111111 | 494a3f00-e966-11ea-84bf-83e48ffdc8ac |                OMG that guy Patrick is such a geek! | 77777777-7777-7777-7777-777777777777

(4 rows)
KVUser@cqlsh:killervideo> select userid, dateOf(commentid) as datetime, videoid, comment from comments_by_user;

 userid                               | datetime                        | videoid                              | comment
--------------------------------------+---------------------------------+--------------------------------------+--------------------------------------
 22222222-2222-2222-2222-222222222222 | 2020-10-07 13:36:14.466000+0000 | 12345678-1234-1111-1111-111111111111 |        I really like this video too!
 11111111-1111-1111-1111-111111111111 | 2020-10-07 13:35:57.036000+0000 | 12345678-1234-1111-1111-111111111111 | Soo many comments for the same video
 11111111-1111-1111-1111-111111111111 | 2020-10-07 13:35:55.655000+0000 | 12345678-1234-1111-1111-111111111111 |           I keep watching this video
 11111111-1111-1111-1111-111111111111 | 2020-10-07 13:35:09.113000+0000 | 12345678-1234-1111-1111-111111111111 |             I so grew up in the 80's

(4 rows)
KVUser@cqlsh:killervideo> INSERT INTO comments_by_video (
               ...   videoid,
               ...   commentid,
               ...   userid,
               ...   comment
               ... )
               ... VALUES(
               ...   12345678-1234-1111-1111-111111111111,
               ...   494a3f00-e966-11ea-84bf-83e48ffdc8ac,
               ...   77777777-7777-7777-7777-777777777777,
               ...   'OMG that guy Patrick is such a geek!'
               ... );
KVUser@cqlsh:killervideo> CREATE TABLE IF NOT EXISTS comments_by_video (
               ...     videoid   uuid,
               ...     commentid timeuuid,
               ...     userid    uuid,
               ...     comment   text,
               ...     PRIMARY KEY ((videoid), commentid)
               ... ) WITH CLUSTERING ORDER BY (commentid DESC);
KVUser@cqlsh:killervideo> UPDATE comments_by_video
               ... SET comment = 'OMG that guy Patrick is on fleek'
               ... WHERE videoid = 12345678-1234-1111-1111-111111111111 AND commentid = 494a3f00-e966-11ea-84bf-83e48ffdc8ac;
KVUser@cqlsh:killervideo> DELETE FROM comments_by_video
               ... WHERE videoid = 12345678-1234-1111-1111-111111111111 AND commentid = 494a3f00-e966-11ea-84bf-83e48ffdc8ac;
KVUser@cqlsh:killervideo> SELECT * FROM comments_by_video;

 videoid                              | commentid                            | comment                                             | userid
--------------------------------------+--------------------------------------+-----------------------------------------------------+--------------------------------------
 08765309-1234-9999-9999-111111111111 | 46f445b0-08a2-11eb-9506-dfc14281f836 | Never thought I'd see a music video about databases | 55555555-5555-5555-5555-555555555555
 12345678-1234-1111-1111-111111111111 | 30a0c9a0-08a2-11eb-9506-dfc14281f836 |                                   Such a killr edit | 22222222-2222-2222-2222-222222222222
 12345678-1234-1111-1111-111111111111 | 24f80410-08a2-11eb-9506-dfc14281f836 |                           This is such a cool video | 11111111-1111-1111-1111-111111111111

(3 rows)
KVUser@cqlsh:killervideo>
KVUser@cqlsh:killervideo> select count(1) from comments_by_video;

 count
-------
     3

(1 rows)

Warnings :
Aggregation query used without partition key

KVUser@cqlsh:killervideo> desc tables;

comments_by_video  comments_by_user  users_by_city

KVUser@cqlsh:killervideo>KVUser@cqlsh:killervideo> show version;
[cqlsh 6.8.0 | DSE 6.8.4.145 | CQL spec 3.4.5 | DSE protocol v2]
KVUser@cqlsh:killervideo> consistency;
Current consistency level is LOCAL_QUORUM.
KVUser@cqlsh:killervideo> show host;
Connected to caas-cluster at caas-cluster-caas-dc-service:9042.
KVUser@cqlsh:killervideo> tracing on;
Now Tracing is enabled
KVUser@cqlsh:killervideo> select * from comments_by_video;

 videoid                              | commentid                            | comment                                             | userid
--------------------------------------+--------------------------------------+-----------------------------------------------------+--------------------------------------
 08765309-1234-9999-9999-111111111111 | 46f445b0-08a2-11eb-9506-dfc14281f836 | Never thought I'd see a music video about databases | 55555555-5555-5555-5555-555555555555
 12345678-1234-1111-1111-111111111111 | 30a0c9a0-08a2-11eb-9506-dfc14281f836 |                                   Such a killr edit | 22222222-2222-2222-2222-222222222222
 12345678-1234-1111-1111-111111111111 | 24f80410-08a2-11eb-9506-dfc14281f836 |                           This is such a cool video | 11111111-1111-1111-1111-111111111111

(3 rows)

Tracing session: bc015790-08a6-11eb-9506-dfc14281f836

 activity                                                                                                                          | timestamp    | source      | source_elapsed | client
-----------------------------------------------------------------------------------------------------------------------------------+----------------------------+-------------+----------------+-------------
                                                                                                                Execute CQL3 query | 2020-10-07 14:09:37.678000 | 10.52.29.97 |              0 | 10.52.2.109
                                                                           Parsing select * from comments_by_video; [CoreThread-0] | 2020-10-07 14:09:37.682000 | 10.52.29.97 |           5278 | 10.52.2.109
                                                                                                Preparing statement [CoreThread-0] | 2020-10-07 14:09:37.683000 | 10.52.29.97 |           6342 | 10.52.2.109
                                                                                       Computing ranges to query... [CoreThread-0] | 2020-10-07 14:09:37.683000 | 10.52.29.97 |           6918 | 10.52.2.109
                        Submitting range requests on 9 ranges with a concurrency of 1 (0.0 rows per range expected) [CoreThread-0] | 2020-10-07 14:09:37.684000 | 10.52.29.97 |           7217 | 10.52.2.109
                    Executing seq scan across 0 sstables for (min(-9223372036854775808), min(-9223372036854775808)] [CoreThread-0] | 2020-10-07 14:09:37.685000 | 10.52.29.97 |           8427 | 10.52.2.109
                                                                              Read 3 live rows and 1 tombstone ones [CoreThread-0] | 2020-10-07 14:09:37.686000 | 10.52.29.97 |           9618 | 10.52.2.109
                                                                              Submitted 1 concurrent range requests [CoreThread-0] | 2020-10-07 14:09:37.686000 | 10.52.29.97 |           9930 | 10.52.2.109
 Didn't get enough response rows; actual rows per range: 0.33333334; remaining rows: 97, new concurrent requests: 1 [CoreThread-0] | 2020-10-07 14:09:37.687000 | 10.52.29.97 |          10551 | 10.52.2.109
                                                                                                                  Request complete | 2020-10-07 14:09:37.692933 | 10.52.29.97 |          14933 | 10.52.2.109


KVUser@cqlsh:killervideo> show session bc015790-08a6-11eb-9506-dfc14281f836;

Tracing session: bc015790-08a6-11eb-9506-dfc14281f836

 activity                                                                                                                          | timestamp    | source      | source_elapsed | client
-----------------------------------------------------------------------------------------------------------------------------------+----------------------------+-------------+----------------+-------------
                                                                                                                Execute CQL3 query | 2020-10-07 14:09:37.678000 | 10.52.29.97 |              0 | 10.52.2.109
                                                                           Parsing select * from comments_by_video; [CoreThread-0] | 2020-10-07 14:09:37.682000 | 10.52.29.97 |           5278 | 10.52.2.109
                                                                                                Preparing statement [CoreThread-0] | 2020-10-07 14:09:37.683000 | 10.52.29.97 |           6342 | 10.52.2.109
                                                                                       Computing ranges to query... [CoreThread-0] | 2020-10-07 14:09:37.683000 | 10.52.29.97 |           6918 | 10.52.2.109
                        Submitting range requests on 9 ranges with a concurrency of 1 (0.0 rows per range expected) [CoreThread-0] | 2020-10-07 14:09:37.684000 | 10.52.29.97 |           7217 | 10.52.2.109
                    Executing seq scan across 0 sstables for (min(-9223372036854775808), min(-9223372036854775808)] [CoreThread-0] | 2020-10-07 14:09:37.685000 | 10.52.29.97 |           8427 | 10.52.2.109
                                                                              Read 3 live rows and 1 tombstone ones [CoreThread-0] | 2020-10-07 14:09:37.686000 | 10.52.29.97 |           9618 | 10.52.2.109
                                                                              Submitted 1 concurrent range requests [CoreThread-0] | 2020-10-07 14:09:37.686000 | 10.52.29.97 |           9930 | 10.52.2.109
 Didn't get enough response rows; actual rows per range: 0.33333334; remaining rows: 97, new concurrent requests: 1 [CoreThread-0] | 2020-10-07 14:09:37.687000 | 10.52.29.97 |          10551 | 10.52.2.109
                                                                                                                  Request complete | 2020-10-07 14:09:37.692933 | 10.52.29.97 |          14933 | 10.52.2.109


KVUser@cqlsh:killervideo>
