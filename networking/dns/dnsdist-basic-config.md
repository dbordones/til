# DNSDIST.conf

```
getPool(""):unsetCache()

newServer({address="192.168.100.1:1053", useClientSubnet=true,reconnectOnUp=true,useProxyProtocol=true,qps=25, order=2, name="B1"})
newServer({address="192.168.100.1:2053", useClientSubnet=true,reconnectOnUp=true,useProxyProtocol=true,qps=25, order=2, name="B2"})
newServer({address="10.1.96.6:53", useClientSubnet=true,reconnectOnUp=true,useProxyProtocol=true,qps=12, order=6, name="B5"})
setServerPolicy(roundrobin)

addLocal('127.0.0.1:53', {interface="lo"})
addLocal('10.1.96.5:53', {interface="ens7"})
addLocal('45.76.12.156:53', {interface="ens3"})

local dbr = dynBlockRulesGroup()
dbr:setQueryRate(80, 10, "Exceeded query rate", 60)
dbr:setRCodeRate(DNSRCode.NXDOMAIN, 20, 10, "Exceeded NXD rate", 60)
dbr:setRCodeRate(DNSRCode.SERVFAIL, 20, 10, "Exceeded ServFail rate", 60)
dbr:setQTypeRate(DNSQType.ANY, 20, 10, "Exceeded ANY rate", 60)
dbr:setResponseByteRate(10000, 10, "Exceeded resp BW rate", 60)
dbr:excludeRange({"10.1.96.0/28", "127.0.0.1/32" })

function maintenance()
  dbr:apply()
end

recursivepc = newPacketCache(10000, {maxTTL=86400, minTTL=300, temporaryFailureTTL=60, staleTTL=60, dontAge=false})
getPool(""):setCache(recursivepc)
setStaleCacheEntriesTTL(3600)

addAction(MaxQPSIPRule(8, 32, 64, 12), DelayAction(100))
addAction(RegexRule("[0-9]{5,}"), DelayAction(750)) -- milliseconds

addResponseAction(RCodeRule(dnsdist.NXDOMAIN), DelayResponseAction(1000))
addCacheHitResponseAction(RCodeRule(dnsdist.NXDOMAIN), DelayResponseAction(1000))

setECSOverride(true)
setECSSourcePrefixV4(32)
setECSSourcePrefixV6(128)

setACL({'::/0','0.0.0.0/0'})
```
