# SOC Home Lab: Detekcja zagrożeń i automatyczna reakcja w Wazuh SIEM

## 1. Architektura i cel projektu
Celem projektu było zbudowanie od podstaw środowiska laboratoryjnego SOC w celu nauki zbierania logów, tworzenia reguł korelacji, analizy incydentów oraz automatycznej reakcji na zagrożenia (SOAR).

* **Wirtualizacja:** Oracle VM VirtualBox (sieć mostkowana)
* **Serwer SIEM:** Wazuh Server (Manager, Indexer, Dashboard)
* **Monitorowany host:** Windows 11 + Wazuh Agent + Sysmon

---

## 2. Przeprowadzone scenariusze śledcze (Case Studies)

### Scenariusz 1: Wykrywanie ukrytych poleceń PowerShell (`-EncodedCommand`) i dekodowanie
* **Symulacja:** Uruchomienie ukrytego procesu PowerShell z komendą zakodowaną w Base64 (`MITRE T1059.001`).
* **Detekcja i analiza:** Przechwycenie pełnej linii poleceń (`commandLine`) oraz drzewa procesów (`parentImage`) dzięki integracji **Sysmon (Event ID 1)** z Wazuh. Odkodowanie ładunku w **CyberChef** (Base64 + UTF-16LE) do jawnej postaci `whoami; hostname`.
  (Projekt/reconnaissance/reconnaissance.png)

### Scenariusz 2: Własna reguła korelacji XML – skanowanie aplikacji WWW (`FakeBank`)
* **Symulacja:** Postawienie lokalnego serwera HTTP (Python) oraz przeprowadzenie automatycznego skanowania ukrytych podstron (generowanie serii błędów HTTP `404`).
* **Detekcja:** Przekierowanie logów `access.log` do agenta Wazuh oraz napisanie własnej reguły korelacji w `local_rules.xml` (`ID 100202`, `frequency="5"`, `timeframe="15"`), podnoszącej priorytet incydentu do poziomu 13 przy wykryciu wzorca skanowania.
*(Tutaj przeciągnij Zrzut 4)*

### Scenariusz 3: Monitorowanie integralności plików (FIM)
* **Symulacja:** Podrzucenie, modyfikacja i usunięcie pliku `okup.txt` w monitorowanym katalogu `C:\Bank` (symulacja działania ransomware / sabotażu).
* **Detekcja:** Konfiguracja modułu `syscheck` w trybie `realtime="yes"`, rejestrująca w czasie rzeczywistym zmiany sum kontrolnych (MD5/SHA256) oraz konto użytkownika wykonującego operację.
*(Tutaj przeciągnij Zrzut 2)*

### Scenariusz 4: Wykrywanie ataku Brute-Force i automatyczna blokada (Active Response)
* **Symulacja:** Przeprowadzenie serii nieudanych logowań sieciowych z zewnętrznego hosta (`Event ID 4625`).
* **Reakcja (SOAR):** Konfiguracja mechanizmu **Active Response** (`route-null.exe`) podpiętego pod regułę `60122` (*Multiple Windows Logon Failures*), automatycznie odcinającego adres IP atakującego na 600 sekund.

---

## 3. Własny Dashboard analityczny
Zbudowano dedykowany widok w Wazuh Dashboard prezentujący licznik incydentów krytycznych (`rule.level >= 10`), rozkład taktyk według matrycy **MITRE ATT&CK** oraz zestawienie najczęstszych alertów.
*(Tutaj przeciągnij Zrzut 1)*
