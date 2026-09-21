# 《用 Wireshark 讀封包》練習用封包檔

這是書籍 **《用 Wireshark 讀封包 ― 看懂該從哪裡看起》**（遠藤　曉）中會開啟的、**由作者在自己的環境擷取的封包檔**。

內文中出現的數值，全部都是從這些檔案實際量出來的。請把同樣的檔案放在手邊，一邊讀一邊把同樣的畫面叫出來。

> 日本語版の README は [README.md](README.md) にあります。

## 一覽

| 檔案 | 章 | 內容 | 封包數 | 大小 | 期間 |
|---|---|---|---:|---:|---|
| `packets/http.pcapng` | 第 1 章 | 用 `curl` 對測試用 Web 伺服器存取一次 | 24 | 2 kB | 16 秒 |
| `packets/nmap.pcapng` | 第 1 章 | `nmap` 的 SYN 掃描（open / closed / filtered） | 1,991 | 148 kB | 8 秒 |
| `packets/dns-icmp.pcapng` | 第 1 章 | 先做名稱解析再 `ping`（UDP 與 ICMP 的樣貌） | 56 | 7 kB | 34 秒 |
| `packets/https-dns-icmp_macOS.pcapng` | 第 1 章 | 在 macOS 上 `nslookup` → `ping` → `curl https` | 156 | 109 kB | 10 秒 |
| `packets/notepad.pcapng` | 第 5 章 | 從簡易 Web 伺服器下載一個執行檔 | 26 | 329 kB | 0.02 秒 |
| `packets/https-gmail.pcap` | 第 3 章 | 執行一次 `curl https://www.gmail.com/`（TLS 解密用） | 38 | 11 kB | 0.08 秒 |

要確認檔案有沒有齊，可以用 `capinfos`。應該會跟上面表格的封包數一致。

```
capinfos -c packets/*.pcapng
```

## 是怎麼擷取的

在 MacBookAir 上跑 VMware Fusion Pro，裡面放兩台虛擬機，如此而已。沒有用到特別的設備。

```
VM-WIN (172.16.28.158)  --HTTP :8000-->  VM-KALI (172.16.28.81)
   curl                                    python3 -m http.server 8000
                                           nmap / tcpdump
                                              |
                                              +--> MacBookAir (172.16.28.1)
                                                   用 Wireshark 解析
```

Kali 那一側開三個終端機視窗，分成**伺服器角色**（`python3 -m http.server 8000`）、**操作角色**（`nmap`、`ping`、`nslookup`）、**記錄角色**（`sudo tcpdump -nn -i eth0 -w 檔名`）。

**同樣的環境誰都做得出來。**書籍的附錄 C 把架構、指令、換算方式全部寫出來了。**自己擷取的紀錄能學到的更多** ― 因為你知道自己做了什麼，所以能判斷畫面上出現的東西是「如預期」還是「出乎預料」。

## 使用時的注意事項

- **請關掉名稱解析再開啟。** 在 Wireshark 的 `View > Name Resolution` 把 *Resolve Network Addresses* 關掉，避免解析過程中自己的電腦跑去查 DNS。
- **顯示的時間會依開啟的電腦的時區而變。** 檔案裡記錄的是以 UTC 為基準的值，**甚至日期都會改變**。回答時間或寫進報告的時候，請務必註明是哪一個時區的值。
- **IP 位址、連接埠、時間都是作者環境的值。** 你自己執行同樣的指令，得到的會是不同的值。要看的不是值本身，而是封包的排列方式與結構。
- **`https-gmail.pcap` 的金鑰紀錄檔（key log）沒有一起公開。** 打開這個檔案，只會看到加密的狀態（停在 `tls`）。因為**金鑰紀錄檔就是能把那段通訊整個剝光的鑰匙本身**。解密後的樣子刊在書籍的 §3.7。想自己試的話，請設定 `SSLKEYLOGFILE`，**用自己的通訊解自己的密**。
- **從 `notepad.pcapng` 取得出來的 `notepad.exe` 是 Windows 內建的記事本**，不是惡意程式。第 5 章是練習「從 pcap 還原執行檔」手順的一章，但手順不論對象是檢體還是記事本，一個位元組都不會變，所以用無害的二進位檔代替。
- 檔名雖然是 `.pcapng`，但用 `tcpdump -w` 擷取的那四個**內容是傳統的 pcap 格式**（用 `capinfos -t` 就看得出來）。Wireshark 和 tshark 都能直接開啟。書中把這件事當成「副檔名不保證檔案的內容」的實例來處理。

## 關於本書

《用 Wireshark 讀封包 ― 看懂該從哪裡看起》，是一本幫你脫離**「封包是打開了，可是不知道該從哪裡看起」**這個狀態的書。

- 第 1 章　窺探封包 ― 分層與 Wireshark
- 第 2 章　用統計掌握全貌 ― 大型 pcap 的走法
- 第 3 章　深入挖掘 ― 進階用法
- 第 4 章　tcpdump / tshark 實務參考
- 第 5 章　還原檔案 ― 把一次 HTTP 傳輸追到底
- 附錄 A 練習用封包檔／附錄 B 術語小辭典／附錄 C 作者的實驗環境

## 聯絡方式

本書除了上述檔案之外，還會使用規模較大的封包檔。需要那些檔案的讀者、發現內容有誤的讀者，或是覺得「這裡應該要有畫面」的讀者，請與作者聯絡。**我們會個別處理。**

**endoakira808@gmail.com**

## 關於使用

這個儲存庫裡的封包檔，是作者在自己的環境擷取的。**學習、研習、讀書會等用途請自由使用。**如果能註明出處書名，我們會很感謝。
