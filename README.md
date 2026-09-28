☁️ Disposable Windows Cloud Computer

一個透過 GitHub Actions + Docker + KVM/QEMU + Windows 11 LTSC + ngrok 建立的拋棄式雲端 Windows 電腦。

本專案參考、使用、結合了dockurr/windows的軟體，在此感謝dockurr。

每次啟動都會建立一個新的 GitHub-hosted Ubuntu runner，並在其中自動啟動 Windows 11 LTSC 虛擬機。

Windows 啟動完成後，可以透過瀏覽器使用 Web Viewer 操作 Windows。

每次工作階段預計維持約四小時，Workflow 結束後 GitHub-hosted runner 會被回收，該次環境也會隨之銷毀。

«⚠️ 本專案僅供合法、合理的短期測試及個人用途。請遵守 GitHub、ngrok、Microsoft 及其他相關服務的使用條款。使用者必須對自己在環境中的所有行為負責。»

---

✨ 功能特色

- 🖥️ 自動建立 Windows 11 LTSC 雲端電腦：透過 GitHub Actions 自動建立 Ubuntu runner，並利用 Docker、KVM 與 QEMU 建立 Windows 11 LTSC 虛擬機。
- 🪟 Windows 11 LTSC：啟動後提供一個可自由使用的 Windows 11 LTSC 環境。
- ⚙️ 可自訂硬體資源：可以直接修改 "docker-compose-win.yml" 中的 CPU、RAM 及虛擬磁碟大小。
- 🌐 透過 ngrok 公開 Web 介面：將 Windows Web Viewer 的 "8006" 埠透過 ngrok 公開到網際網路，使用瀏覽器即可連線。
- ⚠️ Web 介面可能存在延遲：Web Viewer 的操作體驗會受到網路延遲、頻寬及封包品質影響，可能出現較高延遲、畫面更新較慢或操作不順暢的情況。
- ⏰ 限時使用：Workflow 會在初始化完成後維持約四小時，結束後 GitHub-hosted runner 會被回收。

---

🖥️ 這台雲端電腦是什麼？

這不是一台永久在線的 VPS，而是一台臨時的 Windows 雲端電腦。

基本架構如下：

GitHub Actions
      │
      ▼
GitHub-hosted Ubuntu Runner
      │
      ▼
Docker
      │
      ▼
dockurr/windows
      │
      ▼
KVM / QEMU
      │
      ▼
Windows 11 LTSC
      │
      ├── Web Viewer : 8006
      │
      └── RDP : 3389

GitHub Actions 會依序：

1. 建立 GitHub-hosted Ubuntu runner。
2. 安裝 Docker。
3. 下載 Windows Docker Compose 設定。
4. 啟動 Windows 11 LTSC 容器。
5. Windows 虛擬機透過 KVM/QEMU 執行。
6. 啟動 ngrok。
7. 將 Windows Web Viewer 的 "8006" 埠公開到網際網路。
8. 維持工作階段約四小時。
9. Workflow 結束後，GitHub 回收 runner。

---

📦 Windows 虛擬機配置

目前 "docker-compose-win.yml" 預設配置：

資源| 預設值
CPU| 4 核心
RAM| 8 GB
虛擬磁碟| 80 GB
Windows| Windows 11 LTSC
Web Viewer| 8006
RDP| 3389

目前 Compose 使用：

environment:
  VERSION: "11l"
  DISK_SIZE: "80G"
  RAM_SIZE: "8G"
  CPU_CORES: "4"

修改硬體配置

直接編輯：

docker-compose-win.yml

例如：

environment:
  VERSION: "11l"
  DISK_SIZE: "80G"
  RAM_SIZE: "8G"
  CPU_CORES: "4"

可以依照需求修改：

- "RAM_SIZE"：Windows 虛擬機 RAM
- "CPU_CORES"：Windows 虛擬 CPU 核心數
- "DISK_SIZE"：Windows 虛擬磁碟大小

例如：

RAM_SIZE: "12G"
CPU_CORES: "4"
DISK_SIZE: "100G"

«⚠️ 實際可使用的硬體資源受到 GitHub-hosted runner 限制。提高這些數值並不代表 GitHub 一定會提供更多實體 CPU、RAM 或儲存空間，設定過高甚至可能導致虛擬機無法正常啟動。»

«⚠️ "DISK_SIZE" 是 Windows 虛擬磁碟的配置大小，不代表 GitHub runner 一定具有相同大小的可用實體磁碟空間。»

修改軟體配置

找到docker-compose-win.yml

裡面有

- USERNAME

可以做修改，如果有其他變數的需求可前往dockur/windows來源倉庫

*https://github.com/dockur/windows*

---

📋 使用前準備

你需要：

1. 一個 GitHub 帳號。
2. 一個 GitHub Repository。
3. 一個 ngrok 帳號及 Authtoken。
4. 啟用 GitHub Actions。
5. 確認 Repository 可以執行 GitHub Actions。

---

🚀 安裝與設定

1. 建立 GitHub Repository

建立一個新的 Repository，並將本專案的檔案上傳到其中。

專案主要包含：

.github/
└── workflows/
    └── docker-win.yml

docker-compose-win.yml
README.md

其中：

.github/workflows/docker-win.yml

是 GitHub Actions 工作流程。

---

2. 設定 ngrok Secret

這個專案需要 ngrok Authtoken 才能建立隧道。

請務必設定以下 Secret，否則 Workflow 會因缺少憑證而停止。

進入：

Repository
→ Settings
→ Secrets and variables
→ Actions
→ New repository secret

設定：

欄位| 值
Name| "ngrok_userkey"
Secret| 你的 ngrok Authtoken

取得 Authtoken 可以前往：

https://dashboard.ngrok.com/get-started/your-authtoken

請勿將 Authtoken 直接寫入公開程式碼、Workflow 或 README。

---

3. 啟用 GitHub Actions

確認 Repository 已經允許執行 GitHub Actions。

進入：

Repository
→ Settings
→ Actions
→ General

確認 Actions 權限設定允許此 Repository 執行 Workflow。

如果 Workflow 被停用，也可以進入：

Actions

頁面重新啟用。

«如果 Repository 受到組織政策限制，可能需要組織管理員允許 GitHub Actions。»

---

▶️ 啟動 Windows 雲端電腦

本專案支援兩種啟動方式。

方法一：手動啟動

1. 進入 GitHub Repository。
2. 點選上方的 "Actions"。
3. 在左側選擇 "Start windows"。
4. 點選 "Run workflow"。
5. 選擇 "main" 分支。
6. 點選 "Run workflow"。

Workflow 便會開始執行。

---

方法二：Push 自動啟動

目前 Workflow 也設定了：

on:
  workflow_dispatch:
  push:
    branches:
      - main

因此當 "main" 分支有新的 push 時，也會自動啟動。

例如：

git add .
git commit -m "Update"
git push origin main

推送成功後便會觸發新的 Workflow。

«⚠️ 每次符合 "push" 條件的提交都可能啟動新的 runner，請避免不必要的重複執行。»

---

⚙️ GitHub Actions 執行流程

目前 Workflow 大致依照以下順序執行：

步驟| 工作內容
Install Docker| 安裝 Docker
Download Docker Compose file| 下載 Windows Compose 設定
Start Docker containers| 啟動 Windows 容器
Check Docker| 檢查容器是否正在執行
Install ngrok| 安裝 ngrok
Configure and Start ngrok| 設定 Authtoken 並啟動 ngrok
Ready| 顯示環境已完成初始化
Keep Actions Running for 4 Hours| 維持工作階段四小時

初始化完成後會看到：

The computer is ready!

這代表初始化步驟已完成。

«⚠️ 這個訊息不代表 Windows 已經完全開機，也不代表 ngrok 公開網址一定可以正常連線。Windows 虛擬機仍可能需要一段時間才能進入完整桌面。»

---

🌐 Web Viewer

Windows 容器會提供 Web Viewer：

8006

Workflow 使用：

ngrok http 8006

將這個 Web 介面透過 ngrok 公開到網際網路。

因此使用者可以透過瀏覽器連線到 Windows。

⚠️ Web Viewer 可能很慢

Web Viewer 是透過網路傳輸 Windows 桌面畫面，因此實際體驗會受到：

- GitHub runner 網路
- 使用者自己的網路
- ngrok 路由
- 網路延遲
- 頻寬
- 封包遺失
- Windows 畫面更新量

等因素影響。

因此：

«Web Viewer 可能出現較高延遲、低 FPS、畫面更新較慢或滑鼠鍵盤操作延遲。»

這是遠端桌面透過瀏覽器傳輸的正常限制，並不代表 Windows 虛擬機本身一定有問題。

---

🔗 如何取得 ngrok 公開網址？

目前 Workflow 不會將 ngrok 公開網址自動輸出到 GitHub Actions 日誌。

啟動成功後，請前往你的 ngrok Dashboard 查看目前建立的 Tunnel / Endpoint。

https://dashboard.ngrok.com/

Workflow 使用：

ngrok http 8006

因此公開的 Endpoint 會轉發至 runner 的：

8006

也就是 Windows Web Viewer。

«⚠️ 請不要將公開網址分享給不需要使用的人，因為該網址可以直接連線到你的 Windows Web Viewer。»

---

🖥️ RDP

目前 Docker Compose 也已經映射 Windows 的 RDP 埠：

ports:
  - 3389:3389/tcp
  - 3389:3389/udp

因此 Windows 虛擬機本身可以使用 RDP。

不過目前 Workflow 只啟動：

ngrok http 8006

所以目前專案主要使用的是：

Browser
  ↓
ngrok HTTPS
  ↓
8006
  ↓
Windows Web Viewer

目前並沒有透過 ngrok 公開 3389 RDP。

未來如果加入 ngrok TCP Tunnel，理論上可以使用 RDP 連線，以改善部分 Web Viewer 的延遲問題。

---

⏰ 運行時間及環境回收

目前 Workflow 使用：

sleep 14400

"14400" 秒等於：

4 小時

另外 Workflow 設定：

timeout-minutes: 360

也就是：

最大 6 小時

⚠️ 兩者不是同一件事

"timeout-minutes: 360" 是 GitHub Actions Workflow 的最大執行時間。

而：

sleep 14400

是本專案主動維持環境的等待時間。

因此：

四小時是等待時間，不是從 GitHub 開始執行的瞬間起算。

例如：

GitHub Actions 啟動
      ↓
安裝 Docker
      ↓
下載映像 / 啟動 Windows
      ↓
啟動 ngrok
      ↓
Windows 初始化
      ↓
開始 4 小時等待
      ↓
sleep 14400 結束
      ↓
Workflow 結束
      ↓
GitHub 回收 runner

初始化所花費的時間會額外增加整體 Workflow 執行時間。

---

🗑️ 為什麼是拋棄式的？

每次 Workflow 執行都會使用新的 GitHub-hosted runner。

Workflow 結束後，GitHub 會回收該 runner。

因此這台 Windows 電腦不是永久存在的。

請注意：

- 不要將重要檔案只儲存在 Windows 虛擬機內。
- 不要預期下一次 Workflow 可以保留上一台 Windows 的資料。
- 重要資料應在工作階段結束前自行備份。
- 不應將本專案當成永久 VPS 或長期伺服器。

---

🚫 使用規範與責任聲明


這是一台臨時的雲端電腦。

請合理使用 GitHub Actions、runner、Docker、ngrok 及其他相關資源，並遵守所有適用的服務條款與法律。

嚴禁將本專案用於以下行為：

- ⛏️ 挖礦：不得利用 runner 的 CPU、GPU 或其他資源進行加密貨幣挖礦。
- 🦠 惡意活動：不得進行惡意程式散布、未經授權的入侵、攻擊或掃描。
- 📧 垃圾訊息：不得用於垃圾郵件、垃圾訊息或其他濫用服務。
- 🔓 未經授權的存取：不得利用這台電腦存取他人未授權的系統或資料。
- 🚨 規避平台政策：不得利用本專案規避 GitHub、ngrok 或其他服務的使用限制、封鎖或執行政策。

使用者自行承擔責任

本專案只提供自動化環境建立方式，不提供任何違規行為的授權或保證。

使用者必須對自己在 Windows 環境中執行的：

- 程式
- 命令
- 網路連線
- 檔案
- 其他操作

自行負責。

如果使用者違反 GitHub、ngrok、Microsoft 或其他相關服務的使用條款，可能導致 Workflow 被停止、使用額度受到限制、服務被中止或帳號受到處分。

四小時後自動結束 Workflow，不代表可以避免帳號被限制或封鎖。

且本專案不確保照著使用規則使用就不會被封號，透過actions執行雲端電腦本身就是灰色地帶，不保證官方不會修改使用規範，請依照當下的Github使用規範。



---

⚠️ 常見問題

1. Windows 啟動很久

Windows 需要在 Docker 容器中的 QEMU 虛擬機內啟動。

第一次啟動可能需要一段時間。

請先確認 GitHub Actions 中：

docker ps

可以看到 "windows" 容器正在執行。

---

2. Web Viewer 很卡

這不一定代表 Windows 本身效能不足。

Web Viewer 的速度會受到網路狀況影響。

如果看到：

- FPS 很低
- 滑鼠延遲
- 鍵盤輸入延遲
- 畫面更新慢

可能是網路或 Web Viewer 傳輸造成的。

---

3. ngrok Secret 未設定

如果出現：

Error: ngrok_userkey secret is not set!

請確認 Repository Secrets 中存在：

ngrok_userkey

注意名稱必須完全相同，包括：

- 大小寫
- 底線
- 拼字

---

4. Docker 容器無法啟動

請檢查 GitHub Actions 的：

Start Docker containers

步驟。

可能原因包括：

- Docker Compose 設定錯誤。
- Docker image 無法下載。
- GitHub runner 不支援所需功能。
- "/dev/kvm" 無法使用。
- VM 資源配置過高。
- Windows 虛擬機本身啟動失敗。

---

5. ngrok 已啟動但無法連線

請確認：

- Windows 容器正在執行。
- Windows Web Viewer 正常啟動。
- Docker 有映射 "8006:8006"。
- ngrok 程序正在執行。
- ngrok Dashboard 中有正常建立 Endpoint。

---

6. Workflow 提前結束

請檢查 GitHub Actions 執行紀錄。

可能原因：

- Docker 安裝失敗。
- Docker image 下載失敗。
- Windows 容器啟動失敗。
- ngrok 啟動失敗。
- GitHub Actions 執行時間或額度限制。
- Workflow 被手動取消。
- GitHub runner 發生問題。

四小時並不是絕對保證的執行時間。

---

🔐 安全注意事項

這個專案會將 Windows Web Viewer 暴露到網際網路。

因此請注意：

- 不要在 Windows 中輸入重要帳號密碼。
- 不要在其中儲存敏感個人資料。
- 不要將 ngrok 公開網址隨意分享。
- 不要將 ngrok Authtoken 寫入 Repository。
- 不要將 Secret 提交到 Git。
- 不要把這個環境當成可信任的私人電腦。

尤其是：

«只要 Web Viewer 被公開，就應該把它視為一台可以從網際網路連線的電腦。»

---

📁 專案結構

.
├── .github/
│   └── workflows/
│       └── docker-win.yml
│
├── docker-compose-win.yml
│
└── README.md

---

🧩 技術架構

本專案主要使用：

- GitHub Actions
- GitHub-hosted Ubuntu runner
- Docker
- Docker Compose
- "dockurr/windows"
- KVM
- QEMU
- Windows 11 LTSC
- ngrok

架構：

┌───────────────────────────────┐
│       GitHub Actions          │
└───────────────┬───────────────┘
                │
                ▼
┌───────────────────────────────┐
│ GitHub-hosted Ubuntu Runner   │
└───────────────┬───────────────┘
                │
                ▼
┌───────────────────────────────┐
│            Docker             │
│                               │
│       dockurr/windows         │
│              │                │
│          KVM / QEMU           │
│              │                │
│       Windows 11 LTSC         │
└──────────────┬────────────────┘
               │
               ├──────── 8006 ────────► ngrok ────────► Internet
               │
               └──────── 3389 ────────► RDP

---

📜 License

本專案採用 MIT License。

你可以：

- 個人使用
- 商業使用
- 修改程式碼
- 建立衍生作品
- 再發布
- 整合到其他專案
- 在任何國家或地區使用



«⚠️ 本專案使用的第三方軟體、Docker image、Windows 及其他元件，仍然受到各自的授權條款限制。MIT License 僅適用於本專案本身的程式碼，不會改變第三方軟體的授權條件。»

Copyright (c) 2026 syuankai
popcat1020622@gmail.com

商標與第三方權利聲明

- GitHub、Windows、Docker、Ubuntu、ngrok、QEMU、KVM 等名稱、商標及相關標誌均屬其各自權利人所有。
- 本專案與上述公司、組織或產品之間沒有任何官方隸屬、贊助、認證或背書關係，除非另有明確說明。
- 本專案僅使用相關軟體及服務所提供的公開功能，不主張擁有任何第三方商標、名稱、標誌或其他智慧財產權。
- 第三方軟體、服務及其相關內容仍受其各自的授權條款、服務條款及智慧財產權規範約束；本專案的授權條款不會取代或擴張任何第三方授權。
- 使用者應自行確認其使用方式符合相關服務的授權條款、服務條款及適用法律。

詳見 Repository 中的 "LICENSE" 檔案。
