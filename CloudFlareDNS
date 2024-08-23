#!/usr/bin/env python3

import nmcli
import argparse

parser = argparse.ArgumentParser(prog="cf", description="Simple CloudFlare DNS Tool")

parser.add_argument("command", help="command to issue", choices=["enable", "disable", "toggle", "status"], nargs="?")
parser.add_argument("-s", "--silent", help="silences all printing", action="store_true")
args = parser.parse_args()

enabled = False
ignoreAuto = "no"

def load():
	global enabled, ignoreAuto
	con = nmcli.connection.show("Mines")
	ignoreAuto = con["ipv4.ignore-auto-dns"]
	i = 1
	DNS = []
	while f"IP4.DNS[{i}]" in con:
		DNS.append(con[f"IP4.DNS[{i}]"])
		i += 1

	enabled = '1.1.1.1' in DNS

def downUpCon():
	try:
		nmcli.connection.down("Mines")
	except nmcli._exception.NotExistException:
		pass
	nmcli.connection.up("Mines")

def enable():
	global enabled
	load()
	if enabled:
		if not args.silent:
			print("Already enabled, exiting")
		exit()
	nmcli.connection.modify("Mines", {"ipv4.ignore-auto-dns": "yes"})
	nmcli.connection.modify("Mines", {"ipv4.dns": "1.1.1.1 1.0.0.1"})
	downUpCon()

def disable():
	global enabled
	load()
	if not enabled:
		if not args.silent:
			print("Already disabled, exiting")
		exit()
	nmcli.connection.modify("Mines", {"ipv4.ignore-auto-dns": "no"})
	nmcli.connection.modify("Mines", {"ipv4.dns": ""})
	downUpCon()

if args.command == "enable":
	enable()
if args.command == "disable":
	disable()
if args.command == "toggle":
	if enabled:
		disable()
	else:
		enable()

if not args.silent:
	load()
	print("Status:")
	print(f"- Ignore DHCP DNS:    {ignoreAuto}")
	print(f"- CloudFlare enabled: {'yes' if enabled else 'no'}")
