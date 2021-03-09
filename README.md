# tel-schema-for-asterisk.
Patch, makes  Asterisk able to receive calls  with tel:  schema in From SIP header.

INSTALLTION:

Download the chan_sip.patch file into the asterisk source directory,  to the channels/   folder. 
Run:
<pre>
root@localhost# patch < chan_sip.patch
patching file chan_sip.c
root@localhost#
</pre>                                      


Problem description :
The INVITE like : 
```
INVITE sip:+123456789@myid.telecom.au SIP/2.0
Via: SIP/2.0/UDP 192.168.11.2:5060;branch=z9hG4bKk013kj2osqkfpihfgsoo32kfq;Role=3;Hpt=8e82_36;TRC=ffffffff-ffffffff
Call-ID: asbcaydtt7a4fa4mdqcdef2qqq7q87yrqddf@192.168.11.2
From: <tel:0987654321;noa=national;srvattri=national>;tag=srcrey77
To: <sip:+123456789@192.168.11.2;user=phone>
CSeq: 1 INVITE
Allow: INVITE,ACK,OPTIONS,BYE,CANCEL,REGISTER,INFO,PRACK,SUBSCRIBE,NOTIFY,UPDATE,MESSAGE,REFER
Contact: <sip:192.168.11.2:5060;Dsp=ecba-200;Hpt=nw_5d_60461155_c24ec_ex_8e82_16;CxtId=4;TRC=ffffffff-ffffffff>
Max-Forwards: 61
Supported: timer,100rel
User-Agent: Huawei SoftX3000 V600R013
Session-Expires: 1800;refresher=uac
Min-SE: 600
P-Asserted-Identity: <sip:0987654321@192.168.11.2;user=phone>
Privacy: none

```
won't be accepted by the chan_sip of any asterisk versions at the current moment. 
Issue is: request is missing the domain under From: sip header, which results in logs the following error: 

```
NOTICE[30382][C-00000016]: chan_sip.c:19304 check_user_full: From address missing 'sip:', using it anyway
ERROR[30382][C-00000016]: chan_sip.c:19314 check_user_full:<b> Empty domain name in FROM header</b>
NOTICE[30382][C-00000016]: chan_sip.c:26314 handle_request_invite: Failed to authenticate device <tel:0987654321;noa=national;srvattri=national>;tag=srcrey77
```

The Patch was created to re-use  "P-Asserted-Identity"  header  for such requests and accept the call. 
Its not the best solution, and cover only INVITE  requests,   but it works.


