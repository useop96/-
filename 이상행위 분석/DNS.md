# DNS query를 이용하여 이상징후 분별 조사



# SIEM으로 7가지 악의적인 DNS Traffic을 확인하는 방법

## 1. High DNS bytes outbound from same source

악의적인 공격자들은 DNS를 활용하여 데이터를 유출한다. 

53번 Port(DNS)로 대량의 데이터를 전송시 발생한다.

###  To detect this we can make a simple rule

- Firewall Log Version
  
Destination Port = 53

Log Source Type = Your firewall type

Same source IP, over 300,000 bytes message size (Sum / Count) within 1 minute.


- DNS Version

Log Source = Your DNS logs

Same source IP, over 300,000 bytes question length (Sum / Count) within 1 minute.

You will need to add legitimate domains to the whitelist, these will depend on your business.

감지 : 1분에 같은 Source IP로부터 dns query의 Sum / count가 300,000byte가 넘어갈 경우

해결법 : 적절한 도메인을 화이트리스트에 추가, 설정하여 해결

## 2. HIGH DNS REQUESTS FROM SAME SOURCE

같은 source로부터 높은 DNS 요청시

짧은 시간에 하나의 소스 IP에서 대량의 DNS 요청을 수신하는 경우

###  To detect this we can make a simple rule

- Firewall Log Version

Destination Port = 53

Log Source Type = Your firewall type

Same source IP, over 1000 port 53 connections (Sum / Count) within 1 minute.

Exclusion for your DNS servers as a source.

You will need to add legitimate source / destination combinations to the white list, these will depend on your business.

- DNS Version

Log Source = Your DNS logs

Same source IP, over 1000 requests (Sum / Count) within 1 minute.

You will need to add legitimate domains to the whitelist, these will depend on your business.

감지 : 동일한 source IP에서 1분 이내에 1000개 이상의 요청이 발생한 경우


해결법 : 적절한 도메인을 화이트리스트에 추가, 설정하여 해결(1번과 동일)

## 3. IODINE TOOL DETECTION (HIGH RATE OF NULL REQUESTS)

아이오딘 툴이라는 DNS 유출 도구를 감지한다.

해당 툴은 DNS로 IPv4를 통해 방화벽을 무시하고 데이터 유출이 가능하다.

아이오딘 툴은 정기적으로 탐지할 수 있는 패턴을 생성함으로 보안을 유지

- DNS Version

Log Source = Your DNS logs

Same source IP, over 50 requests (Sum / Count) within 1 minute

Record Type = NULL

Some whitelisting may be required as already mentioned above.

감지 : 동일한 Source IP에서 1분에 50개의 요청이 발생하고 Record Type이 NULL인 경우

해결법 : 위에서 언급한 바와 같이 whitelist로 해결할 수 있다.

## 4. DO – EXFILTRATION (HIGH RATE OF TXT REQUESTS)

D0 - EXFILTRATION의 DNS 유출 툴로 TXT record를 사용하여 데이터를 추출할 수 있다. 

- DNS Version

Log Source = Your DNS logs

Same source IP, over 50 requests (Sum / Count) within 1 minute.

Record Type = TXT

Some whitelisting may be required as already mentioned above.

감지 : 동일한 Source IP에서 1분간 50개의 요청이 발생하고 Record Type이 TXT인 경우

해결법 : 위에서 언급한 바와 같이 whitelist로 해결

## 5. COBALT STRIKE BEACON DETECTION


This blog post from 3 years ago from Gigamon is the basis of our next rule – https://atr-blog.gigamon.com/2017/07/25/footprints-of-fin7-tracking-actor-patterns-part-1/

This blog post from 3 years ago from Gigamon is the basis of our next rule – https://atr-blog.gigamon.com/2017/07/25/footprints-of-fin7-tracking-actor-patterns-part-1/

- DNS Version

Log Source = Your DNS logs

Queried domain is ‘aaa.stage.*’ or ‘post.1*’

감지 : query된 domain이 'aaa.state.*' 이거나 'post.1*' 인 경우

## 6. DETECT QUERIES WITH BASE64 ENCODED STRINGS

Base64로 인코딩된 문자열로 query를 검색하는 경우

악의적인 공격자들은 Base64로 인코딩된 문자열로 query를 보낸다. 

방화벽을 통과하여 유출되는 내용을 숨기기 위해 해당 작업을 수행한다.

- DNS version

Log Source = Your DNS logs

Query matches expression ‘*==.*’

감지 : query의 식이 ‘*==.*’와 일치한 경우

This tool from https://github.com/krmaxwell/dns-exfiltration is a good example. The way of detecting this is quite high on resources, but does work.

## 7. DETECT BACKDOOR USING DNS TXT QUERIES

DNS TXT 쿼리를 이용하여 백도어 검색을 하는 경우

이전까지의 데이터 유출의 과정과 다르게 침투를 위한 용도이다.

이 경우에는 DNS TXT 쿼리를 사용하여 전송되는 문자열을 검색하는 규칙을 설정하는 것

- DNS version

Log Source = Your DNS logs

Record Type = TXT

Answer contains ‘IEX, Invoke-Expression or cmd.exe’

감지 : DNS의 record type이 TXT이고 answer에 IEX, Invoke-Expression 이나 cmd.exe가 포함된 경우









Reference : https://blueteamblog.com/7-ways-to-detect-malicious-dns-traffic-using-siem