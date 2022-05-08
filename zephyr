#!/usr/bin/env python

import sys
import os

EC = "/sys/kernel/debug/ec/ec0/io"
OFFSET = 128
def write_to_ec(addr, value):
		with open(EC , 'r+b') as ec:
				ec.seek(addr)
				ec.write(bytes.fromhex(value[2:]))

def read_from_ec(addr):
		with open (EC , 'r+b') as ec:
				ec.seek(addr)
				dat = ec.read(1).hex()
				return dat

def hex_to_int(hex):
		return int(hex,16)

def int_to_hex(integer):
	tmp = hex(integer)
	if len(tmp) % 2 == 0:
		return tmp
	else:
		return tmp[:2] + '0' + tmp[2:]
		
def print_help():
		print("This is help")

if len(sys.argv) == 1:
	print_help()
	
elif sys.argv[1] == '--help' or sys.argv[1] == '-h':
		print_help()


elif sys.argv[1] == '--battery-threshold' or sys.argv[1] == '-b':
	if sys.argv[2] == '--get' or sys.argv[2] == '-g':
		os.system("modprobe ec_sys")
		threshold = read_from_ec(0xEF)
		res = hex_to_int(threshold) - OFFSET
		print("Battery threshold : " + '%' + str(res))

	elif sys.argv[2] == "--set" or sys.argv[2] == '-s':
		os.system("modprobe ec_sys write_support=1")
		threshold = sys.argv[3]
		write_to_ec(0xEF, int_to_hex( int(threshold) + OFFSET))
		print("Battery threshold set to %" + threshold + " successfully.")
	else:
		print("Command not valid.")

	os.system("rmmod ec_sys")




elif sys.argv[1] == '--fan-speed' or sys.argv[1] == '-f':
	if sys.argv[2] == '--get' or sys.argv[2] == '-g':
		os.system("modprobe ec_sys")
		cpu_cursor = 0x71
		gpu_cursor = 0x8A
		cpu_readings = []
		gpu_readings = []
		while cpu_cursor <= 0x77:
			cpu_readings.append(str(hex_to_int(read_from_ec(cpu_cursor))))
			cpu_cursor = cpu_cursor + 0x01
		while gpu_cursor <= 0x8F:
			gpu_readings.append(str(hex_to_int(read_from_ec(gpu_cursor))))
			gpu_cursor = gpu_cursor + 0x01
		print("CPU fan :" + '\t',end='')
		[print(cpu + '\t',end='') for cpu in cpu_readings]
		print('\n')
		print("GPU fan :" + '\t',end='')
		[print(gpu + '\t', end = '') for gpu in gpu_readings]
		print('\n')
	elif sys.argv[2] == '--set' or sys.argv[2] == '-s':
		os.system("modprobe ec_sys write_support=1")
		with open(sys.argv[3], 'r') as config:
			cpu_config = config.readline().strip().split('\t')
			print(cpu_config)
			gpu_config = config.readline().strip().split('\t')
			cpu_cursor = 0x71
			gpu_cursor = 0x8A
			for cpu in cpu_config:
				write_to_ec(cpu_cursor, int_to_hex(int(cpu)))
				cpu_cursor = cpu_cursor + 0x01
			for gpu in gpu_config:
				write_to_ec(gpu_cursor, int_to_hex(int(gpu)))
				gpu_cursor = gpu_cursor + 0x01
		print("Settings set successfully")
	os.system("rmmod ec_sys")

