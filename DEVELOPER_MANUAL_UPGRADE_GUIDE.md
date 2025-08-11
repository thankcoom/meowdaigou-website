# 開發者手動升級專業版操作指南

## 📋 目錄
1. [前置準備](#前置準備)
2. [快速開始](#快速開始)
3. [詳細操作步驟](#詳細操作步驟)
4. [用戶管理工具](#用戶管理工具)
5. [常見問題處理](#常見問題處理)
6. [Firebase Console 操作](#firebase-console-操作)

## 前置準備

### 1. 設定環境變數
```bash
# 複製環境變數範例檔案
cp .env.example .env

# 編輯 .env 檔案，填入你的 Firebase 配置
nano .env
```

### 2. 安裝相依套件
```bash
# 確保在 mobile-app 目錄下
cd daigou-app/mobile-app

# 安裝 Node.js 相依套件
npm install
```

### 3. 確認 Firebase 權限
確保你的 Firebase 專案有正確的讀寫權限。

## 快速開始

### 升級用戶為專業版（最常用）
```bash
# 使用 email 升級（預設30天）
node scripts/manualUpgradeUser.js user@example.com

# 升級365天
node scripts/manualUpgradeUser.js user@example.com 365

# 永久專業版
node scripts/manualUpgradeUser.js user@example.com -1

# 使用 UID 升級
node scripts/manualUpgradeUser.js abc123def456ghi 30
```

### 查看所有用戶
```bash
# 列出所有用戶
node scripts/manageUsers.js list

# 只看專業版用戶
node scripts/manageUsers.js pro

# 只看免費版用戶
node scripts/manageUsers.js free
```

## 詳細操作步驟

### 步驟 1：找到用戶資訊

#### 方法 A：從 App 內獲取
1. 請用戶登入 App
2. 進入「設定」頁面
3. 查看「專屬ID」（這就是 UID）
4. 記下 Email

#### 方法 B：使用管理工具
```bash
# 查找特定用戶
node scripts/manageUsers.js find user@example.com
```

#### 方法 C：從 Firebase Console
1. 登入 [Firebase Console](https://console.firebase.google.com)
2. 選擇你的專案
3. 進入 Authentication → Users
4. 找到用戶的 UID

### 步驟 2：升級用戶

```bash
# 標準升級（30天）
node scripts/manualUpgradeUser.js user@example.com

# 自訂天數
node scripts/manualUpgradeUser.js user@example.com 90

# 永久專業版（用於內部測試）
node scripts/manualUpgradeUser.js user@example.com -1
```

執行後會看到：
```
🚀 開始升級用戶為專業版...
🔍 搜尋用戶: user@example.com

📋 當前用戶資訊:
   UID: abc123def456
   Email: user@example.com
   名稱: 王小明
   當前方案: free

⚡ 正在升級用戶...

✅ 升級成功！

🎉 升級後資訊:
   UID: abc123def456
   Email: user@example.com
   方案: 專業版
   到期日: 2025/02/07
   每日計算: 無限制
   匯出功能: 已啟用
   進階設定: 已啟用
```

### 步驟 3：驗證升級結果

1. **請用戶重新登入 App**
   - 這是最重要的步驟！
   - 用戶必須登出再登入才能載入新權限

2. **檢查設定頁面**
   - 應該顯示「專業版」標籤
   - 訂閱到期日應該正確顯示

3. **測試專業版功能**
   - 計算次數不受限制
   - 進階設定可以使用
   - 匯出功能已啟用

## 用戶管理工具

### 查看所有用戶統計
```bash
node scripts/manageUsers.js list
```

輸出範例：
```
📊 統計資訊:
   總用戶數: 156
   專業版用戶: 23
   免費版用戶: 133

👥 用戶列表 (全部):
────────────────────────────────────────

1. 王小明
   UID: abc123def456
   Email: xiaoming@example.com
   方案: 專業版 ⭐
   到期日: 2025/12/31 (剩餘: 330天)
   註冊日期: 2024/01/15
   今日計算: 45次

2. 李小華
   UID: def456ghi789
   Email: xiaohua@example.com
   方案: 免費版
   註冊日期: 2024/02/01
   今日計算: 8次
```

### 查找特定用戶詳情
```bash
node scripts/manageUsers.js find user@example.com
```

### 降級用戶（如果需要）
```bash
node scripts/manageUsers.js downgrade user@example.com
```

## 常見問題處理

### Q1: 用戶升級後仍顯示免費版？
**解決方案：**
1. 確認用戶已經登出並重新登入
2. 檢查升級是否成功：
   ```bash
   node scripts/manageUsers.js find user@example.com
   ```
3. 清除 App 快取（設定 → 儲存空間 → 清除快取）

### Q2: 升級失敗，顯示「找不到用戶」？
**解決方案：**
1. 確認 email 拼寫正確
2. 使用 UID 代替 email：
   ```bash
   # 先找到 UID
   node scripts/manageUsers.js list
   
   # 使用 UID 升級
   node scripts/manualUpgradeUser.js USER_UID_HERE 30
   ```

### Q3: 如何批量升級多個用戶？
**創建批量升級腳本：**
```bash
# 創建用戶列表檔案 users.txt
echo "user1@example.com" >> users.txt
echo "user2@example.com" >> users.txt
echo "user3@example.com" >> users.txt

# 批量升級
while read email; do
  node scripts/manualUpgradeUser.js "$email" 365
  sleep 1  # 避免太快
done < users.txt
```

### Q4: 如何設定試用期？
```bash
# 7天試用
node scripts/manualUpgradeUser.js user@example.com 7

# 14天試用
node scripts/manualUpgradeUser.js user@example.com 14
```

## Firebase Console 操作

如果你偏好使用圖形介面：

### 1. 登入 Firebase Console
- 網址：https://console.firebase.google.com
- 選擇你的專案

### 2. 找到用戶
- Firestore Database → users 集合
- 搜尋用戶的文檔（使用 UID）

### 3. 手動編輯
點擊編輯圖示，修改以下欄位：
```json
{
  "subscription": "pro",
  "subscriptionExpiry": "2025-12-31T23:59:59.999Z",
  "features": {
    "dailyCalculations": -1,
    "exportEnabled": true,
    "advancedReports": true,
    "prioritySupport": true,
    "customWatermark": true,
    "batchExport": true,
    "multiCurrency": true,
    "advancedSettings": true,
    "isProfessional": true
  }
}
```

### 4. 儲存變更
點擊「更新」按鈕

## 緊急聯絡

如果遇到無法解決的問題：

1. **檢查錯誤日誌**
   ```bash
   # 查看 Firebase 函數日誌
   firebase functions:log
   ```

2. **聯絡開發團隊**
   - 提供用戶 UID
   - 描述問題詳情
   - 附上錯誤截圖

## 安全提醒

⚠️ **重要安全事項：**
- 不要分享 .env 檔案內容
- 定期更新 Firebase 安全規則
- 只給需要的人員升級權限
- 記錄所有手動升級操作

---

最後更新：2025/01/08