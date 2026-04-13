
[외부 PC: Kali (다른 물리 PC)]
        ↓
   (스위치 / 공유기 / 물리 LAN)
        ↓
pfSense (WAN 인터페이스)

pfSense
 ├ WAN (외부)(em0)
 ├ DMZ (웹서버/DB)(em1)
 └ LAN (관리망)(em2)

DMZ:
 └ Web 서버 + DB

LAN:
 └ SIEM