OpenSSL使用文档
===
原文：  
[OpenSSL Command-Line HOWTO by Paul Heinlein](https://www.madboa.com/geek/openssl/)  
![Creative Commons Licenses](https://www.madboa.com/images/cc-by-nc-sa.png)

OpenSSL可以用来执行各种加解密操作。	
本文以具体例子来说明如何使用OpenSSL。

## 目录
* [介绍](#1.0)
	* [查看OpenSSL是什么版本？](#1.1)
	* [查看有哪些命令选项？](#1.2)
	* [查看支持哪些算法？](#1.3)
* [性能测试](#2.0)
	* [如何测试当前系统的运算性能？](#2.1)
	* [如何测试远程连接的耗时？](#2.2)
* [证书](#3.0)
	* [如何生成自签名的证书？](#3.1)
	* [如何生成VeriSign的证书请求？](#3.2)
	* [如何测试一个新证书？](#3.3)
	* [如何导入／导出PKCS#12格式的证书？](#3.4)
* [证书验证](#4.0)
	* [如何验证证书？](#4.1)
	* [OpenSSL都认可哪些证书颁发机构？](#4.2)
	* [如何让某个证书通过OpenSSL认证/校验？](#4.3)
* [命令行客户端和服务端](#5.0)
	* [如何与SMTP服务建立安全连接？](#5.1)
	* [如何与其它服务建立安全连接？](#5.2)
	* [如何使用命令行搭建一个SSL服务端？](#5.3)
* [哈希运算](#6.0)
	* [如何计算一个文件的MD5或SHA1哈希值？](#6.1)
	* [如何签名一个哈希值？](#6.2)
	* [如何验证一个签名的哈希值？](#6.3)
	* [如何创建符合Apache要求的认证信息？](#6.4)
	* [都有哪些可用的哈希算法？](#6.5)
* [加密／解密](#7.0)
	* [如何使用base64编码？](#7.1)
	* [如何简单的加密一个文件？](#7.2)
* [错误](#8.0)
	* [如何了解SSL的错误消息？](#8.1)
* [密钥](#9.0)
	* [如何生成RSA密钥？](#9.1)
	* [如何生成RSA公钥？](#9.2)
	* [如何生成DSA密钥？](#9.3)
	* [如何生成椭圆曲线密钥？](#9.4)
	* [如何解密密钥？](#9.5)
* [密码哈希](#10.0)
	* [如何生成加密风格(crypt-style)的密码哈希值？](#10.1)
	* [如何生成阴影风格(shadow-style)的密码哈希值？](#10.2)
* [素数](#11.0)
	* [如何测试一个数是否为素数？](#11.1)
	* [如何生成一组素数？](#11.2)
* [随机数](#12.0)
	* [如何生成随机数？](#12.1)
* [S/MIME](#13.0)
	* [如何验证签名过的S/MIME消息？](#13.1)
	* [如何加密S/MIME消息？](#13.2)
	* [如何对S/MIME消息签名？](#13.3)
* [延伸阅读](#14.0)
* [欢迎评论](#15.0)

<h2 id="1.0">介绍</h2>

OpenSSL可以用来执行各种加解密操作，你可以写脚本来调用它，也可以直接在命令行中操作。  
有关OpenSSL使用的文档比较分散，所以本文以具体问题来说明如何使用。  
假设你已经安装并配置好了OpenSSL，此外再次说明，文档中都是应用实例，不涉及密码学理论及相关概念。  
如果你不知道什么是哈希运算，这里是不会告诉你的，但如果你想知道怎么用OpenSSL对一个文件进行MD5运算，那来这就对了。

<h3 id="1.1">查看OpenSSL是什么版本？</h3>

使用version选项

	$ openssl version
	OpenSSL 0.9.8zh 14 Jan 2016
跟上-a选项能得到更多的信息：
	
	$ openssl versoin -a
	OpenSSL 0.9.8zh 14 Jan 2016
	built on: May 15 2016
	platform: darwin64-x86_64-llvm
	options:  bn(64,64) md2(int) rc4(ptr,char) des(idx,cisc,16,int) blowfish(idx)
	compiler: -arch x86_64 -fmessage-length=0 -pipe -Wno-trigraphs -fpascal-strings -fasm-blocks -O3 -D_REENTRANT -DDSO_DLFCN -DHAVE_DLFCN_H -DL_ENDIAN -DMD32_REG_T=int -DOPENSSL_NO_IDEA -DOPENSSL_PIC -DOPENSSL_THREADS -DZLIB -mmacosx-version-min=10.6
	OPENSSLDIR: "/System/Library/OpenSSL"

<h3 id="1.2">查看有哪些命令选项？</h3>

内置的查看命令(比如openssl list-standard-commands)只能查出一组，有个技巧是加上一个无效的选项(比如help或-h)，就可以得到所有的结果。

	$ openssl help
	openssl:Error: 'help' is an invalid command.

	Standard commands
	asn1parse      ca             ciphers        crl            crl2pkcs7
	dgst           dh             dhparam        dsa            dsaparam
	ec             ecparam        enc            engine         errstr
	gendh          gendsa         genrsa         nseq           ocsp
	passwd         pkcs12         pkcs7          pkcs8          prime
	rand           req            rsa            rsautl         s_client
	s_server       s_time         sess_id        smime          speed
	spkac          verify         version        x509

	Message Digest commands (see the `dgst' command for more details)
	md2            md4            md5            mdc2           rmd160
	sha            sha1

	Cipher commands (see the `enc' command for more details)
	aes-128-cbc    aes-128-ecb    aes-192-cbc    aes-192-ecb    aes-256-cbc
	aes-256-ecb    base64         bf             bf-cbc         bf-cfb
	bf-ecb         bf-ofb         cast           cast-cbc       cast5-cbc
	cast5-cfb      cast5-ecb      cast5-ofb      des            des-cbc
	des-cfb        des-ecb        des-ede        des-ede-cbc    des-ede-cfb
	des-ede-ofb    des-ede3       des-ede3-cbc   des-ede3-cfb   des-ede3-ofb
	des-ofb        des3           desx           rc2            rc2-40-cbc
	rc2-64-cbc     rc2-cbc        rc2-cfb        rc2-ecb        rc2-ofb
	rc4            rc4-40         seed           seed-cbc       seed-cfb
	seed-ecb       seed-ofb
所谓“Standard commands”就是openssl后跟的第一个命令选项，可以使用同样的技巧查看有哪些可执行的子选项。

	$ openssl dgst -h
	unknown option '-h'
	options are
	-c              to output the digest with separating colons
	-d              to output debug info
	-hex            output as hex dump
	-binary         output in binary form
	-sign   file    sign digest using private key in file
	-verify file    verify a signature using public key in file
	-prverify file  verify a signature using private key in file
	-keyform arg    key file format (PEM or ENGINE)
	-signature file signature to verify
	-binary         output in binary form
	-hmac key       create hashed MAC with key
	-engine e       use engine e, possibly a hardware device.
	-md5            to use the md5 message digest algorithm (default)
	-md4            to use the md4 message digest algorithm
	-md2            to use the md2 message digest algorithm
	-sha1           to use the sha1 message digest algorithm
	-sha            to use the sha message digest algorithm
	-sha224         to use the sha224 message digest algorithm
	-sha256         to use the sha256 message digest algorithm
	-sha384         to use the sha384 message digest algorithm
	-sha512         to use the sha512 message digest algorithm
	-mdc2           to use the mdc2 message digest algorithm
	-ripemd160      to use the ripemd160 message digest algorithm
你也可是使用man openssl查看详细。

<h3 id="1.3">查看支持哪些算法？</h3>

使用ciphers选项，更多用法参见[ciphers的说明文档](https://www.openssl.org/docs/manmaster/apps/ciphers.html)。

	# 列出所有可用的算法
	openssl ciphers -v
	
	# 列出符合TLSv1协议的算法
	openssl ciphers -v -tls1
	
	# 列出高级别加密的算法(密钥长度大于128位)
	openssl ciphers -v 'HIGH'
	
	# 列出使用了AES的高级别加密算法
	openssl ciphers -v 'AES+HIGH'

<h2 id="2.0">性能测试</h2>

<h3 id="2.1">如何测试当前系统的运算性能？</h3>

OpenSSL的开发者在库里内置了测性能的套件，通过speed命令选项来使用。它测试在给定的时间内可以进行多少运算，而不是指定的运算需要多少时间。这样处理是很合理的，因此测试程序在性能好坏的系统上运行的时间不会相差太多。  
只使用speed选项，测试所有算法的性能。

	openssl speed
有两组结果，第一组表示指定的算法每秒可以处理多少字节的数据，第二组表示一次签名／验证花费的时间。  
下面是在2.7GHZ Intel Xeon E5处理器上的测试结果：

	The 'numbers' are in 1000s of bytes per second processed.
	type             16 bytes     64 bytes    256 bytes   1024 	bytes   8192 bytes
	md2               2540.48k     5184.66k     6989.57k     7651.67k     7872.51k
	mdc2                 0.00         0.00         0.00         0.00         0.00
	md4              83248.41k   261068.18k   624212.82k   940529.32k  1128846.68k
	md5              62411.57k   184768.36k   408835.75k   586930.52k   678061.98k
	hmac(md5)        48713.62k   148265.56k   359626.67k   563050.68k   670255.79k
	sha1             68829.72k   195087.40k   431001.51k   623344.42k   729505.79k
	rmd160           38598.59k    96226.86k   183336.45k   235962.71k   257526.44k
	rc4             480093.57k   678565.35k   783765.42k   818297.51k   838205.99k
	des cbc          69500.17k    71184.75k    71491.50k    71641.77k    72010.15k
	des ede3         26433.63k    26717.01k    26772.99k    26788.18k    26907.57k
	idea cbc         95690.28k    99334.17k   100835.40k   100787.54k   100900.86k
	seed cbc         76871.40k    77238.46k    77736.50k    77452.97k    77545.47k
	rc2 cbc          48984.63k    49589.03k    50188.07k    50103.98k    50066.77k
	rc5-32/12 cbc        0.00         0.00         0.00         0.00         0.00
	blowfish cbc    122583.30k   129550.92k   130876.67k   131111.94k   131394.22k
	cast cbc        109471.38k   114523.31k   115934.46k   116200.45k   116331.86k
	aes-128 cbc     128352.23k   138604.76k   141173.42k   142832.25k   142682.79k
	aes-192 cbc     107703.93k   114456.79k   117716.65k   118847.36k   118784.00k
	aes-256 cbc      93374.87k    99521.51k   101198.51k   101382.49k   101635.41k
	camellia-128 cbc    99270.57k   150412.42k   170346.33k   176311.91k   177913.86k
	camellia-192 cbc    85896.60k   117356.52k   128556.97k   132759.72k   133425.83k
	camellia-256 cbc    87351.27k   117695.15k   128972.03k   132130.47k   133455.87k
	sha256           52372.61k   117766.12k   204825.69k   249974.10k   270914.90k
	sha512           41278.19k   165820.37k   258298.69k   365981.70k   419864.58k
	whirlpool        24803.02k    53047.07k    87593.90k   104570.54k   111159.98k
	aes-128 ige     128441.31k   132981.88k   133269.08k   133738.15k   133966.51k
	aes-192 ige     107831.37k   111507.07k   111800.66k   112156.67k   112219.48k
	aes-256 ige      94382.07k    96351.17k    96750.68k    96958.46k    97446.44k
	ghash           888644.92k  1452788.80k  1696788.74k  1763055.96k  1799086.49k
                  	sign    verify    sign/s verify/s
	rsa  512 bits 0.000049s 0.000004s  20547.1 248266.2
	rsa 1024 bits 0.000194s 0.000011s   5146.0  90735.4
	rsa 2048 bits 0.001194s 0.000037s    837.3  27277.1
	rsa 4096 bits 0.008560s 0.000137s    116.8   7324.5
                  	sign    verify    sign/s verify/s
	dsa  512 bits 0.000048s 0.000046s  20667.7  21701.8
	dsa 1024 bits 0.000113s 0.000126s   8831.9   7951.8
	dsa 2048 bits 0.000362s 0.000430s   2762.0   2322.9
                              	sign    verify    sign/s verify/s
 	256 bit ecdsa (nistp256)   0.0001s   0.0004s   9856.1   2524.4
 	384 bit ecdsa (nistp384)   0.0002s   0.0008s   5103.6   1191.7
 	521 bit ecdsa (nistp521)   0.0004s   0.0018s   2679.0    550.3
                              	op      op/s
 	256 bit ecdh (nistp256)   0.0003s   3063.8
 	384 bit ecdh (nistp384)   0.0007s   1447.3
 	521 bit ecdh (nistp521)   0.0015s    666.2
 
 也可以只测试指定的算法：
 
	# 测试RSA的速度
	openssl speed rsa
	
	# 在多核系统中并行两组测试
	openssl speed rsa -multi 2
	
<h3 id="2.2">如何测试远程连接的耗时？</h3>

使用s_time选项可以测试连接性能，直接调用会运行30秒，使用任意的加密算法，统计每秒钟通过SSL握手建立的连接数，结果分为新会话和重用会话两组：

	openssl s_time -connect romete.host:443
	# 译者注：可以拿百度来测试
	openssl s_time -connect baidu.com:443
除了直接调用外，s_time选项也提供了很多测试的子选项

	＃ 只通过新会话获取远端test.html页面
	openssl s_time -connect remote.host:443 -www /test.html -new
	
	# 使用SSL v3和高级别加密算法
	openssl s_time \
		-connect remote.hot:443 -www /test.html -new \
		-ssl3 -cipher HIGH
		
	# 测试不同加密算法的性能，每种算法测试10秒
	IFS=":"
	for c in $(openssl ciphers -ssl3 RSA); do
		echo $C
		openssl s_time -connect remote.host:443 \
			-www / -new -time 10 -cipher $c 2>&1 | \
			grep bytes
		echo
	done
如果你没有可测的启用SSL的服务端，可以使用s_server选项来创建一个。

	＃ 在一台主机上创建服务端，默认使用4433端口
	openssl s_server -cert mycert.pem -www
	
	# 在另一台主机上(可以跟服务端同一台)，运行s_time
	openssl s_time -connect myhost:4433 -www / -new -ssl3
	
<h2 id="3.0">证书</h2>

<h3 id="3.1">如何生成自签名的证书？</h3>

首先考虑是否要对私钥进行加密。    
加密私钥的好处是更加安全，有人拿走了也无法使用。坏处是需要把密码保存在某个文件或每次启动服务时手动输入。  
我个人倾向不加密私钥，这样不用每次手动输入。（当你厌烦输入时，要知道去除密码保护也是可以的。）  
下面的例子会生成一个mycert.pem证书文件，其中证书的有效期是365天，私钥不加密(-ndoes决定的)。

	openssl req \
		-x509 -nodes -days 365 -sha256 \
		-newkey rsa:2048 -keyout mycert.pem -out mycert.pem
		
这个文件中有两部分信息：RSA私钥和证书。

	cat mycert.pem
	-----BEGIN RSA PRIVATE KEY-----
	...
	-----END RSA PRIVATE KEY-----
	-----BEGIN CERTIFICATE-----
	...
	-----END CERTIFICATE-----
使用这个指令后，会要求你回答一系列的问题：国家，省市，公司，职位等等。当回答“Common Name”这个问题要注意点，你可能会填写主机名或域名，如果服务器的真实地址是mybox.mydomain.com，但用户是通过www.mydomain.com来访问的，那么你应填写用户访问的地址。  
可以通过-subj选项来直接写入这些回答，对生成证书来说惟一有用的就是Common Name(CN)字段。

	openssl req \
		-x509 -nodes -days 365 -sha256 \
		-subj '/C=US/ST=Oregon/L=Portland/CN=www.madboa.com' \
		-newkey rsa:2048 -keyout mycert.pem -out mycert.pem
		
<h3 id="3.2">如何生成VeriSign的证书请求？</h3>

向认证机构(比如VeriSign)申请签名证书是相当繁杂和官僚的，在创建证书请求前要先准备好需要的书面文件(营业执照等)。  
上一节说到，你需要考虑是否加密私钥，下面的例子假设不加密，最后会得到两个文件：私钥文件mykey.pem，证书请求文件myreq.pem。

	openssl req \
		-new -sha256 -newkey rsa:2048 -nodes \
		-keyout mykey.pem -out myreq.pem
如果是使用已有的私钥文件生成证书请求，语法更简单点：
	
	openssl req -new -key mykey.pem -out myreq.pem
同样的，你也可以在命令中直接写入要回答的问题：

	openssl req \
		-new -sha256 -newkey rsa:2048 -nodes \
		-subj '/CN=www.mydom.com/O=My Dom. Inc./C=US/ST=Oregon/L=Portland' \
		-keyout mykey.pem -out myreq.pem
在跟像VeriSign这样的机构打交道时，要特别注意在创建证书请求时填写的信息。根据我个人的经验，比如填写组织名字时用and代替&，都会造成申请不通过。  
需要仔细确认证书请求中的签名、组织等信息。
	
	# 验证签名
	openssl req -in myreq.pem -noout -verify -key mykey.pem
	# 检查信息
	openssl req -in myreq.pem -noout -text
将私钥文件保存在安全的地方，要使用VeriSign发回给你证书必须要用到它。证书请求一般是通过VeriSign的线上表单提交。  

<h3 id="3.3">如何测试一个新证书？</h3>

s_server选项提供了一个简单有效的测试方法。下面的例子假设你已经把私钥和证书合并为一个文件(也可以是上面生成的自签名证书)：mycert.pem。  
首先启动测试服务端，默认使用4433端口，可以使用-accept选项修改。

	openssl s_server -cert mycert.pem -www
如果服务端正常启动，没有报错信息，说明证书就可以使用了。  
可以在浏览器里访问测试服务器https://yourserver:4433/。注意是https，打开这个界面可以看到可用的加密算法以及连接相关的统计。如果是自签名的证书，浏览器会提示你是否相信此证书。  
怎么得到远端的证书？  
只要配合使用openssl和sed命令，就能取到远端的证书信息。
	
	#!/bin/sh
	#
	# usage: retrieve-cert.sh remote.host.name [port]
	#
	REMHOST=$1
	REMPORT=${2:-443}

	echo |\
	openssl s_client -connect ${REMHOST}:${REMPORT} 2>&1 |\
	sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p'
也可以把得到信息再交给openssl处理，比如检查证书的日期：

	#!/bin/sh
	#
	for CERT in \
  		www.yourdomain.com:443 \
  		ldap.yourdomain.com:636 \
  		imap.yourdomain.com:993
	do
  		echo |\
  		openssl s_client -connect ${CERT} 2>/dev/null |\
  		sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' |\
  		openssl x509 -noout -subject -dates
	done
怎么查看证书中的信息？  
一个SSL证书包含很多信息：颁发者，有效期，持有者，算法，公钥等。使用x509选项可以查看这些信息，下面的例子假设有一个名为cert.pem的证书文件。  
使用-text选项能同时以可读文本的形式展示信息。  

	openssl x509 -text -in cert.pem
还有一些选项可以指定展示某项信息。

	# 查看颁发者
	openssl x509 -noout -in cert.pem -issuer
	
	# 查看持有者
	openssl x509 -noout -in cert.pem -subject
	
	# 有效期
	openssl x509 -noout -in cert.pem -dates
	
	# 以上全部
	openssl x509 -noout -in cert.pem -issuer -subject -dates
	
	# 哈希值
	openssl x509 -noout -in cert.pem -hash
	
	# MD5指纹
	openssl x509 -noout -in cert.pem -fingerprint
	
	
<h3 id="3.4">如何导入／导出PKCS#12格式的证书？</h3>

PKCS#12格式的证书被许多程序使用，比如微软的IIS，这种证书文件的后缀通常是.pfx。  
创建PKCS#12格式的证书，需要私钥和证书。转换时，你会被要求输入密码(可以为空)来对证书进行加密。  

	# 创建一个包含私钥的自签名证书
	openssl req \
		-x509 -sha256 -nodes -days 365 \
		-newkey rsa:2048 -keyout mycert.pem -out mycert.pem
		
	# 将mycert.pem导出为PKCS#12格式的证书，mycert.pfx
	openssl pkcs12 -export \
		-out mycert.pfx -in mycert.pem \
		-name "My Certificate"
如果你拿到PKCS#12格式的证书和它的密码(如果有的话)，可以把它导出为标准的PEM格式。

	# 无密码情况下
	openssl pkcs12 -in mycert.pfx -out mycert.pem -nodes
	
	# 有密码情况下
	openssl pkcs12 -in mycert.pfx -out mycert.pem

<h2 id="4.0">证书验证</h2>

使用OpenSSL库可以验证授权机构颁发的证书

<h3 id="4.1">如何验证证书？</h3>

使用verify选项来验证证书。

	openssl verify cert.pem
验证成功，会返回“OK”。  

	$ openssl verify mycert.pem
	mycert.pem: /C=CN/ST=JS/L=WX/O=Internet Widgits Pty Ltd/CN=Calle Zhang
	error 18 at 0 depth lookup:self signed certificate
	OK
如果异常会看到错误消息，比如

	证书过期，通常证书都是有有效期的，一般是一年：
	error 10 at 0 depth lookup:certificate has expired
	
	自签名证书：
	error 18 at 0 depth lookup:self signed certificate
	
<h3 id="4.2">OpenSSL都认可哪些证书颁发机构？</h3>

在编译OpenSSL库时需要制定OpenSSL的文件目录(--openssldir参数指定)，这个目录保存着认可的证书颁发机构信息。  
默认的目录是/usr/local/ssl，很多操作系统会把它放到别处，比如/usr/share/ssl(Red Hat/Fedora)，/etc/ssl(Gentoo)，/usr/lib/ssl(Debian)，/System/Library/OpenSSL(Macintosh OS X)。  
使用version选项加上-d查看目录在什么位置

	openssl version -d
这个目录下有个certs子目录，在certs里可能会有三种类型的文件：  
1. 有一个名为cert.pem的大文件，里面包含了许多认可机构(比如VeriSign，Thawte)的证书。  
2. 一些以.pem为后缀的小文件，每个文件包含一个认可机构的一个证书。  
3. 一些奇怪名字的链接文件，比如052eae11.0，通常是指向.pem文件。这个文件名字的第一部分其实是.pem文件里证书的hash值，后缀是序列号，因为多个链接文件可以指向同一证书。以我的Gentoo系统为例，有个指向vsignss.pem的链接文件叫f73e89fd.0，可以肯定里面证书的哈希值就是这个文件名：

	$ openssl x509 -noout -hash -in vsingss.pem
	f73e89fd
当OpenSSL要验证一个证书，它会查看cert.pem里面是否包含这个证书，如果没有再查看这个目录下是否存在以证书哈希值为名的文件，如果存在就验证通过。  
有意思的是，有些程序(比如Sendmail)会在运行时让你指定你信任证书的存储位置。

<h3 id="4.3">如何让OpenSSL信任某个证书？</h3>

把这个证书放入上文说的certs目录下，然后创建一个以哈希值命名的链接文件，下面的脚本可以完成这样的任务：

	#!/bin/sh
	#
	# usage: certlink.sh filename [filename ...]

	for CERTFILE in $*; do
  		# make sure file exists and is a valid cert
  		test -f "$CERTFILE" || continue
  		HASH=$(openssl x509 -noout -hash -in "$CERTFILE")
  		test -n "$HASH" || continue

  		# use lowest available iterator for symlink
  		for ITER in 0 1 2 3 4 5 6 7 8 9; do
    		test -f "${HASH}.${ITER}" && continue
    		ln -s "$CERTFILE" "${HASH}.${ITER}"
    		test -L "${HASH}.${ITER}" && break
  		done
	done
	
<h2 id="5.0">命令行客户端和服务端</h2>

s_client和s_server选项可以启动使用了SSL的命令行客户端和服务端。  
这部分，假设你已经熟悉这些常见的协议：SMTP，HTTP等。

<h3 id="5.1">如何与SMTP服务建立安全连接？</h3>

你可以通过s_client选项测试甚至使用一个使用SSL的SMTP服务端。  
SMTP服务的安全连接使用3个端口：25(TLS)，465(SSL)和587(TLS)。OpenSSL大概是在0.9.7版本之后提供了-starttls选项：
	
	# 25(TLS)端口，587(TLS)端口也是一样的
	openssl s_client -connect remote.host:25 -starttls smtp
	
	# 465(SSL)端口
	openssl s_client -connect remote.host:456
[RFC821](http://www.ietf.org/rfc/rfc0821.txt)建议使用<CRLF>作为行结束符。虽然大多数的邮件服务都是以<LF>或<CRLF>作为行结束符的，但Qmail就不是。如果你想遵从RFC821或是要与Qmail通信，可以使用-crlf选项：

	openssl s_client -connect remote.host:25 -crlf -starttls smtp
	
<h3 id="5.2">如何其它服务建立安全连接？</h3>

与其它服务建立安全连接本质上与上文是相同的操作。截止本文的写作日期，OpenSSL只支持命令行通过TLS连接SMTP服务，如果想连接其它服务，就只能直接使用SSL连接了:

	# HTTPS
	openssl s_client -connect remote.host:443
	
	# LDAPS
	openssl s_client -connect remote.host:636
	
	# IMAPS
	openssl s_client -connect remote.host:993
	
	# POP3S
	openssl s_client -connect remote.host:995

<h3 id="5.3">如何使用命令行搭建一个SSL服务端？</h3>

s_server选项可以搭建一个SSL服务端，仅限测试、调试时使用，如果要在生产环境启用安全连接，可以考虑使用[Stunnel](https://www.stunnel.org)。  
有证书后就可以通过s_server选项搭建服务了，可以搭建多个

	# -www选项表示，服务端返回一个OpenSSL运行状态的HTML页面
	openssl s_server -cert mycert.pem -www
	
	# 使用-WWW选项，可以访问当前目录下的文件。指定了443端口(默认4433端口)。
	openssl s_server -accept 443 -cert mycert.pem -WWW
	
<h2 id="6.0">哈希运算</h2>

在OpenSSL库中，计算哈希通过dgst选项来操作。哈希运算十分常见，都有专门计算哈希的工具。

<h3 id="6.1">如何计算一个文件的MD5或SHA1哈希值？</h3>

使用dgst选项，先使用openssl dgst -h命令来查看有哪些可用的哈希算法。

	# MD5
	openssl dgst -md5 filename
	
	# SHA1
	openssl dgst -sha1 filename
	
	# SHA256
	openssl dgst -sha256 filename

<h3 id="6.2">如何签名一个哈希值？</h3>

如果想确保得到的哈希值不被篡改，可以使用私钥对这个哈希值签名。下面的例子展示对foo-1.23.tar.gz这个文件的SHA256值进行签名：

	# 签名过的哈希值是f00-1.23.tar.gz.sha1
	openssl dgst -sha256 \
		-sign mykey.pem \
		-out foo-1.23.tar.gz.sha1 \
		foo-1.23.tar.gz

<h3 id="6.3">如何验证一个签名的哈希值？</h3>

验证一个签名的哈希值需要原始文件以及签名者的公钥(openssl rsa -in mycert.pem -pubout -out pubkey.pem)。

	# 使用foo-1.23.tar.gz.sha1和pubkey.pem验证foo-1.23.tar.gz
	openssl dgst -sha256 \
		-verify pubkey.pem \
		-signature foo-1.23.tar.gz.sha1 \
		foo-1.23.tar.gz

<h3 id="6.4">如何创建符合Apache要求的认证信息？</h3>

Apache的HTTP哈希认证需要指定的密码格式。Apache提供有htdigest工具，但只能将结果输出到文件。我们可以手动的往本地密码数据库添加认证信息。  
密码数据库的数据格式是比较简单的，有三部分：用户名，授权名，用户名加授权名加密码的MD5值，三部分以冒号分割。下面的脚本使用OpenSSL的dgst选项输出符合Apache要求的认证信息：

	#!/bin/bash

	echo "Create an Apache-friendly Digest Password Entry"
	echo "-----------------------------------------------"

	# get user input, disabling tty echoing for password
	read -p "Enter username: " UNAME
	read -p "Enter Apache AuthName: " AUTHNAME
	read -s -p "Enter password: " PWORD; echo

	printf "\n%s:%s:%s\n" \
  		"$UNAME" \
  		"$AUTHNAME" \
  		$(printf "${UNAME}:${AUTHNAME}:${PWORD}" | openssl dgst -md5)
<h3 id="6.5">都有哪些可用的哈希算法？</h3>
可以使用内置的list-message-digest-commands选项列出可用的哈希算法：
	
	openssl list-message-digest-commands
注意列出的结果可能是过时的。(还是通过openssl dgst -h查看吧)

<h2 id="7.0">加密／解密</h2>

<h3 id="7.1">如何使用base64编码？</h3>

使用enc -base64选项

	# 对文件file.txt里的内容进行base64编码
	openssl enc -base64 -in file.txt
	
	# 将编码结果输出到file.txt.enc
	openssl enc -base64 -in file.txt -out file.txt.enc
通过命令行编码指定的字符串：

	$ echo "encode me" | openssl enc -base64
	ZW5jb2RlIG1lCg==
注意echo会在字符串结尾添加一个换行符，可以使用-n选项去除。当你对一些认证信息编码时，这点很重要。

	$ echo -n "encode me" | openssl enc -base64
	ZW5jb2RlIG1l
使用-d选项可以解码：
	
	$ echo "ZW5jb2RlIG1lCg==" | openssl enc -base64 -d
	encode me

<h3 id="7.2">如何简单的加密一个文件？</h3>

有时候我们只是想简单的通过密码加密一个文件，不想使用专业的工具，也不想创建私钥／证书之类的。  
这是很简单，你需要记住密码以及使用的加密算法。  
首先选一个加密算法，可以通过下面的方法列出可用的算法：

	# ‘Cipher commands’下的列表
	openssl -h
	
	# 算法列表，每行一个
	openssl list-cipher-commands
选完加密算法后，还要决定是否对加密后数据进行base64编码，base64编码后数据方便查看，不编码就是二进制的形式。

	# 使用256位AES算法CBC模式，加密file.txt为file.enc。
	openssl enc -aes-256-cbc -salt -in file.txt -out file.enc
	
	# 对加密后数据进行base64编码(-a或-base64)
	openssl enc -aes-256-cbc -a -salt -in file.txt -out file.enc
解密file.enc

	# 解密二进制file.enc
	openssl enc -d -aes-256-cbc -in file.enc
	
	# 解密base64格式的
	openssl enc -d -aes-256-cbc -a -in file.enc
如果你不想每次加解密时都输入密码，可以直接在命令中提供：

	# 在命令行提供密码
	openssl enc -aes-256-cbc -salt -in file.txt \
 		-out file.enc -pass pass:mySillyPassword
 	
 	# 在一个文件中提供密码
 	openssl enc -aes-256-cbc -salt -in file.txt \
  		-out file.enc -pass file:/path/to/secret/password.txt
  		
<h2 id="8.0">错误</h2>

<h3 id="8.1">如何了解SSL的错误消息？</h3>

查看系统日志，可能会看到一些明显跟OpenSSL或加密有关的错误消息：
	
	sshd[31784]: error: RSA_public_decrypt failed: error:0407006A:lib(4):func(112):reason(106)
	sshd[770]: error: RSA_public_decrypt failed: error:0407006A:lib(4):func(112):reason(106)
了解错误消息的第一步，使用errstr选项解释错误码，错误码在error:和:lib之间，上面的示例是0407006A。

	$ openssl errstr 0407006A
	error:0407006A:rsa routines:RSA_padding_check_PKCS1_type_1:block type is not 01
如果你完整的安装了OpenSSL，可以在开发文档里找到相关信息。

<h2 id="9.0">密钥</h2>

<h3 id="9.1">如何生成RSA密钥？</h3>

使用genrsa选项。

	# 默认1024位密钥，输出到控制台
	openssl genrsa
	
	# 2048位密钥，保存到mykey.pem
	openssl genrsa -out mykey.pem 2048
	
	# 对密钥进行加密
	openssl genrsa -des3 -out mykey.pem 2048
	
<h3 id="9.2">如何生成RSA公钥？</h3>

使用rsa选项根据私钥生产公钥。

	openssl rsa -in mykey.pem -pubout
	
<h3 id="9.3">如何生成DSA密钥？</h3>

创建DSA密钥需要一个参数文件，并且DSA的验证操作同等情况下比RSA慢。所以没有RSA使用的广泛。  
如果只想创建一个DSA密钥，可以通过dsaparam选项：

	# 私钥保存在dsakey.pem中
	openssl dsaparam -noout -out dsakey.pem -genkey 1024
如果想创建多个密钥，可以先创建一个参数文件，创建参数有点耗时，但创建后再生成密钥就很快了。

	# 创建参数保存在dsaparam.pem中
	openssl dsaparam -out dsaparam.pem 1024
	
	# 创建第一个key
	openssl gendsa -out key1.pem dsaparam.pem
	
	# 第二个...
	openssl gendsa -out key2.pem dsaparam.pem
	
<h3 id="9.4">如何生成椭圆曲线密钥？</h3>

椭圆曲线加密在OpenSSL 0.9.8版本加入。通过ecparam选项生成密钥。

	openssl ecparam -out key.pem -name prime256v1 -genkey
	
	# -name后支持的选项列表
	openssl ecparam -list_curves
	
<h3 id="9.5">如何解密密钥？</h3>

也许对每次都输入密码厌烦了，你可以对密钥解密，使用rsa或dsa选项，取决于使用了哪种加密算法。  
如果你有一个加密的RSA密钥文件key.pem，下面的例子是将解密后的密钥存在文件newkey.pem中。

	# 需要输入密钥的密码
	openssl rsa -in key.pem -out newkey.pem
通常情况是在一个文件里存储私钥和公共证书，假如这个文件是mycert.pem，通过以下两步生成不加密的版本newcert.pem

	# 需要输入密钥的密码
	openssl rsa -in mycert.pem -out newcert.pem
	openssl x509 -in mycert.pem >>newcert.pem
	
<h2 id="10.0">密码哈希</h2>

使用passwd选项，可以生成不同格式的密码哈希值。

<h3 id="10.1">如何生成加密风格(crypt-style)的密码哈希值？</h3>

生成一个新的哈希值非常简单：

	$ openssl passwd MySecret
	8E4vqBR4UOYF.
如果知道密码以及运算时用的盐(salt)，就能重现这个哈希值。

	$ openssl passwd -salt 8E MySecret
	8E4vqBR4UOYF.
	
<h3 id="10.2">如何生成阴影风格(shadow-style)的密码哈希值？</h3>

最近的Unix系统开始使用更加安全的哈希策略，基于MD5运算，并且使用8个字符作为盐(传统的加密风格使用2个字符作为盐)。  
加上-1选项，就能生成这样的哈希值：

	$ openssl passwd -1 MySecret
	$1$sXiKzkus$haDZ9JpVrRHBznY5OxB82.
这个风格的哈希值，盐位于第二个和第三个$符之间，上面例子的盐值是sXiKzkus。知道密码和盐值后可以重现这个哈希值。

	$ openssl passwd -1 -salt sXiKzkus MySecret
	$1$sXiKzkus$haDZ9JpVrRHBznY5OxB82.
	
<h2 id="11.0">素数</h2>

当前的加密技术很大程度上是依赖素数的生成和检验。难怪OpenSSL库提供大量的操作来处理素数。应该是从0.9.7e版本开始，加入了prime选项。

<h3 id="11.1">如何测试一个数是否为素数？</h3>

使用prime选项，后面跟要测试的数字。注意OpenSSL的返回数字是十六进制的。

	$ openssl prime 119054759245460753
	1A6F7AC39A53511 is not prime
也可以直接传入十六进制的数。
	
	$ openssl prime -hex 2f
	2F is prime

<h3 id="11.2">如何生成一组素数？</h3>

从OpenSSL 1.0版本开始，可以生成指定长度的素数：

	$ openssl prime -generate -bits 64
	16148891040401035823
	$ openssl prime -generate -bits 64 -hex
	E207F23B9AE52181
1.0之前的版本，就得传入一个范围的数判断哪个符合要求了。

	# define start and ending points
	AQUO=10000
	ADQUEM=10100
	for N in $(seq $AQUO $ADQUEM); do
  		# use bc to convert hex to decimal
  		openssl prime $N | awk '/is prime/ {print "ibase=16;"$1}' | bc
	done
	
<h2 id="12.0">随机数</h2>

<h3 id="12.1">如何生成随机数？</h3>

使用rand选项可以生成二进制或base64格式的随机数。

	# 直接在控制台输出128位base64格式的随机数
	openssl rand -base64 128
	
	# 在指定的文件中输出1024位二进制随机数
	openssl rand -out random-data.bin 1024
	
	# 从浏览器缓存取到半随机子节数据作为种子来生成随机数
	cd $(find ~/.mozilla/firefox -type d -name Cache)
	openssl rand -rand $(find . -type f -printf '%f:') -base64 1024
在类Unix系统中有个/dev/urandom的设备以及GNU的head工具，或者最近版本BSD的head，使用它们可以更有效的生成随机数：

	# 取得32位随机数，以base64编码显示
	head -c 32 /dev/urandom | openssl enc -base64

可以利用strings工具得到比Base64使用更多字符的随机结果：

	# 取得32位随机数，取得可打印的字符，去除空格，替换掉换行符
	echo $(head -c 32 /dev/random | strings -1) | sed 's/[[:space:]]//g'
在某些极端情况下，请确保了解使用random和urandom的优劣之处。可以在Linux和BSD系统的random(4)的man页面，Solaris的random(7D)的man页面查到更多的信息。

<h2 id="13.0">S/MIME</h2>

S/MIME表示安全的发送和接收MIME数据，尤其是自e-mail消息中。已经有相当部分e-mail客户端默认支持S/MIME了，OpenSSL通过smime选项提供命令行S/MIME服务。    
注意[smime的man页面](https://www.openssl.org/docs/manmaster/apps/smime.html)包含了一些特别好的示例。

<h3 id="13.1">如何验证签名过的S/MIME消息？</h3>

使用mail客户端将签名过的S/MIME消息保存为msg.txt。

	openssl smime -verify -in msg.txt
如果发送者的证书是被你的OpenSSL所认可的授权机构签名的，你就能看到mail的头信息，正文以及一行提示Verification successful。  
如果消息被篡改过，会输出错误消息指明哈希值或者签名不匹配：
	
	Verification failure
	23016:error:21071065:PKCS7 routines:PKCS7_signatureVerify:digest
	failure:pk7_doit.c:804:
	23016:error:21075069:PKCS7 routines:PKCS7_verify:signature
	failure:pk7_smime.c:265:
同样，如果发送者的证书验证失败，会输出类似的错误消息：

	Verification failure
	9544:error:21075075:PKCS7 routines:PKCS7_verify:certificate verify
	error:pk7_smime.c:222:Verify error:self signed certificate
多数e-mail客户端会再消息的签名中发送公共证书。在命令行中你可以查看这证书的数据。可以使用smime -pk7out选线导出PKCS#7证书，然后再使用pkcs7选项。这方法虽然笨但确实可用。

	openssl smime -pk7out -in msg.txt | \
	openssl pkcs7 -text -noout -print_certs
也可以将对方的证书保存为指定的文件。

	openssl smime -pk7out -in msg.txt -out her-cert.pem
这时可以把这个证书加入你的OpenSSL信任组中或存在别处。

	openssl smime -verify -in msg.txt -CAfile /path/to/her-cert.pem
	
<h3 id="13.2">如何加密S/MIME消息？</h3>

加入有人发给你她的公共证书，并要求你发送加密过消息给她。她的证书存为her-cert.pem，你要发的消息存为message.txt。  
默认使用比较弱的RC2-40加密方案，只要告诉OpenSSL消息和证书的地址就行。

	openssl smime her-cert.pem -encrypt -in my-message.txt
如果你确定对方有处理SSL的能力，可以指定更安全的算法，比如DES3。

	openssl smime her-cert.pem -encrypt -des3 -in my-message.txt
默认情况下，加密过消息以及mail头信息输出到了控制台。可以使用-out选项输出到文件中。甚至可以利用sendmail直接发送出去。

	openssl smime her-cert.pem \
  		-encrypt \
  		-des3 \
  		-in my-message.txt \
  		-from 'Your Fullname <you@youraddress.com>' \
  		-to 'Her Fullname <her@heraddress.com>' \
  		-subject 'My encrypted reply' |\
	sendmail her@heraddress.com
	
<h3 id="13.3">如何对S/MIME消息签名？</h3>

如果你不需要加密整个消息，只是想对消息做个签名，这样对方可以确定消息的完整性。处理方法跟加密类似，主要的不同是你需要自己的私钥和证书，因为你不可能拿对方的证书去签名。  

	openssl smime \
  		-sign \
  		-signer /path/to/your-cert.pem \
  		-in my-message.txt \
  		-from 'Your Fullname <you@youraddress.com>' \
  		-to 'Her Fullname <her@heraddress.com>' \
  		-subject 'My signed reply' |\
	sendmail her@heraddress.com

<h2 id="14.0">延伸阅读</h2>

建议从OpenSSL的指导文档开始：[asn1parse(1)](http://www.openssl.org/docs/apps/asn1parse.html), [ca(1)](http://www.openssl.org/docs/apps/ca.html), [ciphers(1)](http://www.openssl.org/docs/apps/ciphers.html), [config(5)](http://www.openssl.org/docs/apps/config.html), [crl(1)](http://www.openssl.org/docs/apps/crl.html), [crl2pkcs7(1)](http://www.openssl.org/docs/apps/crl2pkcs7.html), [dgst(1)](http://www.openssl.org/docs/apps/dgst.html), [dhparam(1)](http://www.openssl.org/docs/apps/dhparam.html), [dsa(1)](http://www.openssl.org/docs/apps/dsa.html), [dsaparam(1)](http://www.openssl.org/docs/apps/dsaparam.html), [ec(1)](http://www.openssl.org/docs/apps/ec.html), [ecparam(1)](http://www.openssl.org/docs/apps/ecparam.html), [enc(1)](http://www.openssl.org/docs/apps/enc.html), [errstr(1)](http://www.openssl.org/docs/apps/errstr.html), [gendsa(1)](http://www.openssl.org/docs/apps/gendsa.html), [genpkey(1)](http://www.openssl.org/docs/apps/genpkey.html), [genrsa(1)](http://www.openssl.org/docs/apps/genrsa.html), [nseq(1)](http://www.openssl.org/docs/apps/nseq.html), [ocsp(1)](http://www.openssl.org/docs/apps/ocsp.html), [openssl(1)](http://www.openssl.org/docs/apps/openssl.html), [passwd(1)](http://www.openssl.org/docs/apps/passwd.html), [pkcs12(1)](http://www.openssl.org/docs/apps/pkcs12.html), [pkcs7(1)](http://www.openssl.org/docs/apps/pkcs7.html), [pkcs8(1)](http://www.openssl.org/docs/apps/pkcs8.html), [pkey(1)](http://www.openssl.org/docs/apps/pkey.html), [pkeyparam(1)](http://www.openssl.org/docs/apps/pkeyparam.html), [pkeyutl(1)](http://www.openssl.org/docs/apps/pkeyutl.html), [rand(1)](http://www.openssl.org/docs/apps/rand.html), [req(1)](http://www.openssl.org/docs/apps/req.html), [rsa(1)](http://www.openssl.org/docs/apps/rsa.html), [rsautl(1)](http://www.openssl.org/docs/apps/rsautl.html), [s_client(1)](http://www.openssl.org/docs/apps/s_client.html), [s_server(1)](http://www.openssl.org/docs/apps/s_server.html), [s_time(1)](http://www.openssl.org/docs/apps/s_time.html), [sess_id(1)](http://www.openssl.org/docs/apps/sess_id.html), [smime(1)](http://www.openssl.org/docs/apps/smime.html), [speed(1)](http://www.openssl.org/docs/apps/speed.html), [spkac(1)](http://www.openssl.org/docs/apps/spkac.html), [ts(1)](http://www.openssl.org/docs/apps/ts.html), [tsget(1)](http://www.openssl.org/docs/apps/tsget.html), [verify(1)](http://www.openssl.org/docs/apps/verify.html), [version(1)](http://www.openssl.org/docs/apps/version.html), [x509(1)](http://www.openssl.org/docs/apps/x509.html), [x509v3_config(5)](http://www.openssl.org/docs/apps/x509v3_config.html)。

<h2 id="15.0">欢迎评论</h2>

这篇文档已经在线上十几年了，多数改动是出于我个人的好奇心，但有些关键的改进是由读者主动提出的。所以如果你对这个文档有什么评论或建议，欢迎发送到 [heinlein@madboa.com](mailto:heinlein@madboa.com.)。

