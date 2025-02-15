# Лабораторная работа 11. BGP. Фильтрация
### Цели
1. Настроить фильтрацию в офисе Москва так, чтобы не появилось транзитного трафика(As-path).
2. Настроить фильтрацию в офисе С.-Петербург так, чтобы не появилось транзитного трафика(Prefix-list).
3. Настроить провайдера Киторн так, чтобы в офис Москва отдавался только маршрут по умолчанию.
4. Настроить провайдера Ламас так, чтобы в офис Москва отдавался только маршрут по умолчанию и префикс офиса С.-Петербург.
### 1. Настроить фильтрацию в офисе Москва так, чтобы не появилось транзитного трафика(As-path).
На R14 и R15 применим фильтр, который будет анонсировать R22 и R21 только маршруты из локальной AS (1001):
```
R14#conf t
R14(config)#ip as-path access-list 1 permit ^$
R14(config)#router bgp 1001
R14(config-router)#neighbor 150.150.150.154 filter-list 1 out
R14(config-router)#end
R14#clear ip bgp * soft
```
```
R15#conf t
R15(config)#ip as-path access-list 1 permit ^$
R15(config)#router bgp 1001
R15(config-router)#neighbor 150.150.150.158 filter-list 1 out
R15(config-router)#end
R15#clear ip bgp * soft
```
### 2. Настроить фильтрацию в офисе С.-Петербург так, чтобы не появилось транзитного трафика(Prefix-list).
На R18 применим фильтр, который будет анонсировать R24 и R26 только локальные маршруты из AS2042:
```
R18#conf t
R18(config)#ip prefix-list DENY_UPDATE_to_AS520 seq 10 permit 90.90.91.0/30
R18(config)#ip prefix-list DENY_UPDATE_to_AS520 seq 15 permit 90.90.91.4/30
R18(config)#router bgp 2042
R18(config-router)#neighbor 90.90.91.1 prefix-list DENY_UPDATE_to_AS520 out
R18(config-router)#neighbor 90.90.91.5 prefix-list DENY_UPDATE_to_AS520 out
R18(config-router)#end
R18#clear ip bgp * soft
```
```
R18#
R18#sh ip bgp neighbors 90.90.91.5 advertised-routes
BGP table version is 131, local router ID is 18.18.18.18
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal,
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter,
              x best-external, a additional-path, c RIB-compressed,
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>  90.90.91.0/30    0.0.0.0                  0         32768 i
 *>  90.90.91.4/30    0.0.0.0                  0         32768 i

Total number of prefixes 2
```
```
R18#
R18#sh ip bgp neighbors 90.90.91.1 advertised-routes
BGP table version is 131, local router ID is 18.18.18.18
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal,
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter,
              x best-external, a additional-path, c RIB-compressed,
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>  90.90.91.0/30    0.0.0.0                  0         32768 i
 *>  90.90.91.4/30    0.0.0.0                  0         32768 i

Total number of prefixes 2
```
### 3. Настроить провайдера Киторн так, чтобы в офис Москва отдавался только маршрут по умолчанию.
```
R22#conf t
R22(config)#router bgp 101
R22(config-router)#neighbor 150.150.150.153 default-originate
R22#end
R22#conf t
R22(config)#ip prefix-list DEFAUL_STATIC_ROUTE_for_R14 permit 0.0.0.0/0
R22(config)#router bgp 101
R22(config-router)#neighbor 150.150.150.153 prefix-list DEFAUL_STATIC_ROUTE_for_R14 out
```
### 4. Настроить провайдера Ламас так, чтобы в офис Москва отдавался только маршрут по умолчанию и префикс офиса С.-Петербург.
```
R21#conf t
R21(config)#router bgp 301
R21(config-router)#neighbor 150.150.150.157 default-originate
R21(config-router)#ex
R21(config)#ip prefix-list DEFAULT_ROUTE_for_R15 seq 10 permit 0.0.0.0/0
R21(config)#ip prefix-list DEFAULT_ROUTE_for_R15 seq 15 permit 90.90.91.0/30
R21(config)#ip prefix-list DEFAULT_ROUTE_for_R15 seq 20 permit 90.90.91.4/30
R21(config)#router bgp 301
R21(config-router)#neighbor 150.150.150.157 prefix-list DEFAULT_ROUTE_for_R15 out
R21(config-router)#end
R21#clear ip bgp * soft
```
