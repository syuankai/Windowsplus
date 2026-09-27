
# ☁️ Disposable Cloud Computer

一台透過 GitHub Actions 建立的拋棄式雲端電腦。

利用 GitHub-hosted Ubuntu runner 建立無 GUI 的 Linux 環境，安裝 Docker，啟動指定的容器，並透過 ngrok 提供外部連線。

**每次啟動僅供短時間使用，約四小時後便會結束工作階段，環境隨之銷毀。**

> ⚠️ 本專案僅供合法、合理的短期測試及個人用途。請遵守 GitHub、ngrok 及相關服務的使用條款。使用者必須對自己在環境中的所有行為負責。

---

## ✨ 功能特色

- 🖥️ **拋棄式雲端電腦**：建立臨時 Linux 環境，無須自行準備實體電腦。
- 🐧 **Ubuntu Linux**：使用 GitHub-hosted Ubuntu runner，提供無 GUI 的命令列環境。
- 🐳 **Docker 支援**：自動安裝 Docker Engine 和 Docker Compose。
- 📦 **自動部署**：自動下載指定的 Docker Compose 設定檔並啟動容器。
- 🌐 **ngrok 隧道**：透過 ngrok 將 8006 埠公開到網際網路。
- ⏰ **限時四小時**：啟動完成後等待四小時，接著結束工作流程並回收環境。

---

## 🖥️ 這台雲端電腦是什麼？

這是一台短期使用的 Linux 電腦，並非永久在線的 VPS。

它的基本運作方式如下：

1. GitHub Actions 建立一台 Ubuntu runner。
2. 自動安裝 Docker 和相關工具。
3. 下載 Docker Compose 設定檔，並啟動容器。
4. 安裝並啟動 ngrok，嘗試建立外部連線。
5. 輸出 `The computer is ready!`，表示初始化步驟已完成。
6. 維持工作階段約四小時。
7. 四小時等待結束後，工作流程結束，GitHub 回收 runner。

<blockquote>
  **重要：** 四小時是工作流程中的等待時間，並不是從 GitHub 開始執行工作流程的那一刻起算。初始化、下載及安裝所花費的時間會額外增加整體執行時間。
</blockquote>

### 為什麼是拋棄式的？

每次執行都會使用新的 GitHub-hosted runner。工作流程結束後，該 runner 會被回收，容器及其本機資料也不會保留給下一次執行。

因此：

- 不要將重要檔案只儲存在臨時環境中。
- 不要預期重新啟動後能繼續使用上一個工作階段。
- 重要資料應在工作階段結束前自行備份。
- 不應將這個環境當作永久伺服器使用。

---

## 📋 使用前準備

你需要：

1. 一個 [GitHub](https://github.com/) 帳號。
2. 一個 GitHub Repository。
3. 一個 [ngrok](https://ngrok.com/) 帳號及 Authtoken。
4. 一份適用於 Linux 的 Docker Compose 設定檔。

---

## 🚀 安裝與設定

### 1. 建立 GitHub Repository

建立一個新的 Repository，並將本專案的檔案上傳到其中。

確認專案包含以下檔案：

```text
.github/
└── workflows/
    └── docker.yml

README.md
```

其中 `docker.yml` 是 GitHub Actions 的工作流程設定檔。

### 2. 設定 ngrok Secret

這個專案需要 ngrok Authtoken 才能建立隧道。

**請務必設定 Secret，否則工作流程會因缺少憑證而停止。**

操作步驟：

1. 開啟你的 GitHub Repository。
2. 進入 `Settings`。
3. 在左側選單找到 `Secrets and variables`。
4. 點選 `Actions`。
5. 在 `Secrets` 分頁點選 `New repository secret`。
6. 填寫以下資訊：

| 欄位 | 值 |
|---|---|
| Name | `ngrok_userkey` |
| Secret | 你的 ngrok Authtoken |

7. 點選 `Add secret` 儲存。

取得 Authtoken：

https://dashboard.ngrok.com/get-started/your-authtoken

請勿將 Authtoken 直接寫入公開程式碼或 README。

### 3. 啟用 GitHub Actions

確認 Repository 允許執行 GitHub Actions。

1. 進入 Repository 的 `Settings`。
2. 點選左側的 `Actions` → `General`。
3. 在 `Actions permissions` 區域確認允許執行所需的 Actions。
4. 若工作流程被停用，請到 `Actions` 頁面啟用。

如果 Repository 受到組織政策限制，則需要由組織管理員允許執行。

---

## ▶️ 啟動雲端電腦

本專案支援兩種啟動方式。

### 方法一：手動啟動

1. 進入 GitHub Repository。
2. 點選上方的 `Actions`。
3. 在左側選擇 `Start Docker Environment`。
4. 點選 `Run workflow`。
5. 選擇 `main` 分支。
6. 再次點選 `Run workflow`。

工作流程便會開始執行。

### 方法二：推送程式碼自動啟動

目前工作流程也設定了在 `main` 分支有新的 push 時自動執行。

例如：

```bash
git add README.md
git commit -m "Update README"
git push origin main
```

推送成功後便會觸發工作流程。

**注意：** 每次符合觸發條件的推送都可能啟動新的 runner，請避免不必要的重複執行。

---

## ⚙️ 執行流程

GitHub Actions 會依序執行以下步驟：

| 步驟 | 工作內容 |
|---|---|
| Install Docker | 安裝 Docker Engine |
| Download Docker Compose file | 下載指定的 Compose 設定檔 |
| Start Docker containers | 啟動 Docker 容器 |
| Check Docker | 顯示目前執行中的容器 |
| Install ngrok | 安裝 ngrok |
| Configure and Start ngrok | 設定 Authtoken 並啟動隧道 |
| Ready | 輸出準備完成訊息 |
| Keep Actions Running for 4 Hours | 等待四小時後結束工作流程 |

初始化完成後，日誌會出現：

```text
The computer is ready!
```

這代表初始化步驟已完成，並不代表 ngrok 公開網址一定可以正常連線。

---

## 🌐 ngrok 外部連線

本專案會執行：

```bash
ngrok http 8006
```

ngrok 會嘗試建立公開的 HTTPS 網址，並將外部連線轉發到 runner 的 8006 埠。

目前工作流程尚未自動擷取或顯示 ngrok 的公開網址。

另外，**Docker Compose 必須有正確的埠映射，而且容器內的服務必須監聽 8006 埠**，外部連線才能正常使用。

請勿將含有敏感資料或管理權限的服務直接公開到網際網路。

---

## ⏰ 運行時間及環境回收

目前工作流程的等待時間為：

```bash
sleep 14400
```

`14400` 秒等於四小時。

工作流程的最大執行時間設定為：

```yaml
timeout-minutes: 360
```

也就是六小時。

四小時等待結束後，工作流程會繼續執行後續步驟（如果有的話），並結束工作階段。GitHub-hosted runner 隨後會被回收。

**請注意：**

- 初始化時間不包含在四小時等待時間內。
- GitHub Actions 有整體執行時間限制及使用額度。
- 不保證每次都能完整運行四小時，因為可能遇到工作流程錯誤、平台限制或其他中斷情況。
- 工作流程被取消或失敗時，環境也可能提前結束。

四小時期限是這個專案的使用設計，不代表能保證帳號不會受到任何限制或處分。

---

## 🚫 使用規範與責任聲明

### 請好自為之

這是一台臨時的雲端電腦，請合理使用資源，並遵守 GitHub、ngrok 及其他相關服務的使用規範。

**嚴禁將本專案用於以下行為：**

- ⛏️ **挖礦**：不得利用 runner 的 CPU、GPU 或其他資源進行加密貨幣挖礦。
- 🦠 **惡意活動**：不得進行惡意程式散布、未經授權的入侵、攻擊或掃描。
- 📧 **垃圾訊息**：不得用於垃圾郵件、垃圾訊息或其他濫用服務。
- 🔓 **未經授權的存取**：不得利用這台電腦存取他人未授權的系統或資料。
- 🚨 **規避平台政策**：不得利用本專案規避平台的使用限制、封鎖或其他執行政策。

### 使用者自行承擔責任

本專案僅提供自動化環境建立腳本，不提供任何違規行為的授權或保證。

使用者必須對自己在環境中執行的所有程式、命令、網路連線及其他行為自行負責。

若使用者違反 GitHub、ngrok 或其他相關服務的使用條款，可能面臨工作流程遭停用、使用額度受限、服務中止或帳號處分等後果。

**四小時後自動結束工作流程，不代表可以避免帳號被限制或封鎖。** 使用者仍須遵守所有適用的服務條款及政策。

專案維護者不對使用者的違規操作及其造成的後果負責。

---

## 🛠️ 常見問題

### 1. Docker 安裝失敗

如果出現：

```text
containerd.io : Conflicts: containerd
```

請確認使用的是 Docker 官方安裝腳本，避免混用 Ubuntu 的 `docker.io` 套件與 Docker 官方套件。

### 2. ngrok Secret 未設定

如果出現：

```text
Error: ngrok_userkey secret is not set!
```

請確認 Repository 的 Secrets 中有一個名稱完全相同的 Secret：

```text
ngrok_userkey
```

注意大小寫及底線。

### 3. Docker 容器無法啟動

請檢查 `Start Docker containers` 步驟的執行紀錄。

可能原因包括：

- Compose 設定檔格式錯誤。
- Compose 設定使用 Windows 專用路徑或功能。
- 容器映像檔無法下載。
- 容器本身的啟動設定不正確。

### 4. ngrok 已啟動但無法連線

請確認：

- Docker 容器正常運行。
- 容器內的服務有監聽 8006 埠。
- Docker Compose 有將 8006 埠映射到 runner 主機。
- ngrok 隧道成功建立。

### 5. 工作流程提前結束

請檢查 GitHub Actions 的執行紀錄，確認是否發生安裝失敗、下載失敗、容器啟動失敗或其他錯誤。

也請確認工作流程沒有超出 GitHub 的執行時間及使用額度限制。

---

## 📄 License

請依照此 Repository 實際採用的授權條款使用及修改本專案。
