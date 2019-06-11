# -*- coding=utf-8 -*-

import psutil
import redis
import os


def get_local_address(ip):
    for net_name,info in psutil.net_if_addrs().items():
        for addr in info:
            if str(addr.address).startswith(ip_head):
                return addr.address


def recon(Host, Port, Db):
    return redis.StrictRedis(host=Host, port=Port, db=Db)


if __name__ == '__main__':
    vip1_list = []
    vip_list = []
    ip_head = "10.3"
    local_ip = get_local_address(ip_head)
    Host = "127.0.0.1"
    Port = 9003
    Db = 0
    r = recon(Host, Port, Db)
    for net_name,info in psutil.net_if_addrs().items():
        for nmask in info:
            if nmask.netmask == "255.255.255.255":
                vip_list.append(nmask.address)
    if os.path.exists('/usr/local/zabbix/sbin/keepalived_vip.log') or os.path.exists('/usr/local/zabbix/sbin/keepalived_vip_status.log'):
        if os.path.exists('/usr/local/zabbix/sbin/keepalived_vip.log'):
            with open('/usr/local/zabbix/sbin/keepalived_vip.log','r') as f:
                v_list = f.readlines()
            for vip1 in v_list:
                vip1_list.append(vip1.strip('\n'))
            if len(v_list) == len(vip_list) and vip1_list == vip_list:
                print "1"
                exit()
        if not vip_list and not r.smembers(local_ip):
            print "1"
            exit()
        if not vip_list and not os.path.exists('/usr/local/zabbix/sbin/keepalived_vip.log') and r.smembers(local_ip):
            print "0"
            exit()
        if vip_list and not os.path.exists('/usr/local/zabbix/sbin/keepalived_vip.log') and len(vip_list) == 1 and len(list(r.smembers(local_ip))) == 2:
            print "0"
            exit()
        if vip_list and not os.path.exists('/usr/local/zabbix/sbin/keepalived_vip.log'):
            print "2"
            for vip in vip_list:
                r.sadd(str(local_ip),str(vip))
            exit()
        with open('/usr/local/zabbix/sbin/keepalived_vip.log','r') as f:
            v_list = f.readlines()
        if vip_list and os.path.exists('/usr/local/zabbix/sbin/keepalived_vip.log') and len(vip_list) > len(v_list):
            print "2"
            for vip in vip_list:
                r.sadd(str(local_ip),str(vip))
            exit()
        elif vip_list and os.path.exists('/usr/local/zabbix/sbin/keepalived_vip.log') and len(vip_list) < len(list(r.smembers(local_ip))):
            print "0"
            exit()
        if vip_list and os.path.exists('/usr/local/zabbix/sbin/keepalived_vip.log') and len(vip_list) == len(list(r.smembers(local_ip))):
            print "1"
            exit()
        if not vip_list and os.path.exists('/usr/local/zabbix/sbin/keepalived_vip.log') and len(list(r.smembers(local_ip))) == 2 or len(list(r.smembers(local_ip)))== 1:
            print "0"
            exit()

    elif vip_list:
        with open('/usr/local/zabbix/sbin/keepalived_vip.log',"w") as f:
            for vip in vip_list:
                r.sadd(str(local_ip), str(vip))
                f.write(vip+"\n")
        print "1"
    else:
        with open('/usr/local/zabbix/sbin/keepalived_vip_status.log','w') as f:
            f.write(str(local_ip)+"info:vip is null!!"+"\n")
        print "1"
