---
title: 在 Linux 上的 Azure App Service 中建置 Ruby 和 MySQL Web 應用程式 | Microsoft Docs
description: 了解如何取得在 Azure 中運作的 Ruby 應用程式，並連線至 Azure 中的 MySQL 資料庫。
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
ms.service: app-service-web
ms.workload: web
ms.devlang: ruby
ms.topic: tutorial
ms.date: 12/21/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: a4a422e62940a535a6303339bd5712654881d304
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2018
---
# <a name="build-a-ruby-and-mysql-web-app-in-azure-app-service-on-linux"></a>在 Linux 上的 Azure App Service 中建置 Ruby 和 MySQL Web 應用程式

[Linux 上的 App Service](app-service-linux-intro.md) 使用 Linux 作業系統提供可高度擴充、自我修復的 Web 主機服務。 本教學課程示範如何建立 Ruby Web 應用程式，並將它連線到 MySQL 資料庫。 完成後，Linux 上將有一個在 App Service 上執行的 [Ruby on Rails](http://rubyonrails.org/) 應用程式。

![在 Azure App Service 中執行的 Ruby on Rails 應用程式](./media/tutorial-ruby-mysql-app/complete-checkbox-published.png)

在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 在 Azure 中建立 MySQL 資料庫
> * 將 Ruby on Rails 應用程式連線到 MySQL
> * 將應用程式部署至 Azure
> * 將資料模型更新並將應用程式重新部署
> * 來自 Azure 的串流診斷記錄
> * 在 Azure 入口網站中管理應用程式

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先決條件

若要完成本教學課程：

* [安裝 Git](https://git-scm.com/)
* [安裝 Ruby 2.3](https://www.ruby-lang.org/documentation/installation/)
* [安裝 Ruby on Rails 5.1](http://guides.rubyonrails.org/v5.1/getting_started.html)
* [下載並啟動 MySQL](https://dev.mysql.com/doc/refman/5.7/en/installing.html) 

## <a name="prepare-local-mysql"></a>準備本機 MySQL

在此步驟中，您可以在本機 MySQL 伺服器中建立資料庫，供您在本教學課程中使用。

### <a name="connect-to-local-mysql-server"></a>連線至本機 MySQL 伺服器

在終端機視窗中，連線到您的本機 MySQL 伺服器。 您可使用這個終端機視窗來執行本教學課程中的所有命令。

```bash
mysql -u root -p
```

如果系統提示您輸入密碼，請輸入 `root` 帳戶的密碼。 如果您不記得根帳戶密碼，請參閱 [MySQL︰如何重設根密碼](https://dev.mysql.com/doc/refman/5.7/en/resetting-permissions.html)。

如果您的命令執行成功，表示您的 MySQL 伺服器正在執行。 如果沒有，請遵循 [MySQL 後續安裝步驟](https://dev.mysql.com/doc/refman/5.7/en/postinstallation.html)，確定您已啟動本機 MySQL 伺服器。

輸入 `quit` 即可結束您的伺服器連線。

```sql
quit
```

<a name="step2"></a>

## <a name="create-a-ruby-on-rails-app-locally"></a>在本機上建立 Ruby on Rails 應用程式
在此步驟中，您會取得 Ruby on Rails 範例應用程式、設定其資料庫連線，並在本機執行。 

### <a name="clone-the-sample"></a>複製範例

在終端機視窗中，使用 `cd` 移至工作目錄。

執行下列命令來複製範例存放庫。

```bash
git clone https://github.com/Azure-Samples/rubyrails-tasks.git
```

使用 `cd` 移至您複製的目錄。 安裝必要的套件。

```bash
cd rubyrails-tasks
bundle install --path vendor/bundle
```

### <a name="configure-mysql-connection"></a>設定 MySQL 連線

在存放庫中開啟 _config/database.yml_，然後提供本機 MySQL 根使用者名稱和密碼 (第 13 行)。 如果您已使用 [Homebrew](https://brew.sh/) 這類工具安裝 MySQL，則檔案中的認證已設定為預設值 (也就是 `root` 和空白密碼)。

```txt
default: &default
  adapter: mysql2
  encoding: utf8
  pool: <%= ENV.fetch("RAILS_MAX_THREADS") { 5 } %>
  username: root
  password:
  socket: /tmp/mysql.sock
```

### <a name="run-the-sample-locally"></a>在本機執行範例

執行 [Rails 移轉](http://guides.rubyonrails.org/active_record_migrations.html#running-migrations)來建立應用程式所需的資料表。 若要查看移轉中會建立哪些資料表，請查看 Git 存放庫中的 _db/migrate_ 目錄。

```bash
rake db:create
rake db:migrate
```

執行應用程式。

```bash
rails server
```

在瀏覽器中，瀏覽至 `http://localhost:3000`。 在頁面中新增幾項工作。

![Ruby on Rails 成功連線至 MySQL](./media/tutorial-ruby-mysql-app/mysql-connect-success.png)

若要停止 Rails 伺服器，請在終端機中輸入 `Ctrl + C`。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-mysql-in-azure"></a>在 Azure 中建立 MySQL

在此步驟中，您會在[適用於 MySQL 的 Azure 資料庫 (預覽)](/azure/mysql) 中建立 MySQL 資料庫。 稍後，您會將 Ruby on Rails 應用程式設定為連線至此資料庫。

### <a name="create-a-resource-group"></a>建立資源群組

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)] 

### <a name="create-a-mysql-server"></a>建立 MySQL 伺服器

使用 [`az mysql server create`](/cli/azure/mysql/server?view=azure-cli-latest#az_mysql_server_create) 命令，在適用於 MySQL 的 Azure 資料庫 (預覽) 中建立伺服器。

在下列命令中，在您看見 _&lt;mysql_server_name>_ 預留位置的地方，取代成您自己的 MySQL 伺服器名稱 (有效字元有 `a-z`、`0-9`、`-`)。 這個名稱是 MySQL 伺服器主機名稱 (`<mysql_server_name>.mysql.database.azure.com`) 的一部分，必須是全域唯一的。

```azurecli-interactive
az mysql server create --name <mysql_server_name> --resource-group myResourceGroup --location "North Europe" --admin-user adminuser --admin-password My5up3r$tr0ngPa$w0rd!
```

建立 MySQL 伺服器後，Azure CLI 會顯示類似下列範例的資訊：

```json
{
  "administratorLogin": "adminuser",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "<mysql_server_name>.database.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/<mysql_server_name>",
  "location": "northeurope",
  "name": "<mysql_server_name>",
  "resourceGroup": "myResourceGroup",
  ...
}
```

### <a name="configure-server-firewall"></a>設定伺服器防火牆

使用 [`az mysql server firewall-rule create`](/cli/azure/mysql/server/firewall-rule?view=azure-cli-latest#az_mysql_server_firewall_rule_create) 命令，建立 MySQL 伺服器的防火牆規則來允許用戶端連線。 當起始 IP 和結束 IP 都設為 0.0.0.0 時，防火牆只會為其他 Azure 資源開啟。 

```azurecli-interactive
az mysql server firewall-rule create --name allAzureIPs --server <mysql_server_name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

### <a name="connect-to-production-mysql-server-locally"></a>在本機連線到生產環境 MySQL 伺服器

在終端機視窗中，連線至 Azure 中的 MySQL 伺服器。 使用您先前為 _&lt;mysql_server_name>_ 指定的值。

```bash
mysql -u adminuser@<mysql_server_name> -h <mysql_server_name>.mysql.database.azure.com -P 3306 -p
```

當系統提示您輸入密碼時，使用您建立資料庫伺服器時指定的 _My5up3r$tr0ngPa$w0rd!_。

### <a name="create-a-production-database"></a>建立生產環境資料庫

在 `mysql` 提示中，建立資料庫。

```sql
CREATE DATABASE sampledb;
```

### <a name="create-a-user-with-permissions"></a>建立具有權限的使用者

建立名為 railsappuser 的資料庫使用者，並將 `sampledb` 資料庫的所有權限賦予給它。

```sql
CREATE USER 'railsappuser' IDENTIFIED BY 'MySQLAzure2017'; 
GRANT ALL PRIVILEGES ON sampledb.* TO 'railsappuser';
```

輸入 `quit` 結束伺服器連線。

```sql
quit
```

## <a name="connect-app-to-azure-mysql"></a>將應用程式連線至 Azure MySQL

在此步驟中，您會將 Ruby on Rails 應用程式連線至您在適用於 MySQL 的 Azure 資料庫 (預覽) 中建立的 MySQL 資料庫。

<a name="devconfig"></a>

### <a name="configure-the-database-connection"></a>設定資料庫連接

在存放庫中，開啟 config/database.yml。 在檔案最下方，使用下列程式碼取代生產變數。 針對 &lt;mysql_server_name> 預留位置，使用您建立的 MySQL 伺服器名稱。

```txt
production:
  <<: *default
  host: <%= ENV['DB_HOST'] %>
  port: 3306
  database: <%= ENV['DB_DATABASE'] %>
  username: <%= ENV['DB_USERNAME'] %>
  password: <%= ENV['DB_PASSWORD'] %>
  sslca: ssl/BaltimoreCyberTrustRoot.crt.pem
```

儲存變更。

> [!NOTE]
> `sslca` 已新增並指向範例存放庫中現有的 .pem 檔案。 根據預設，用於 MySQL 的 Azure 資料庫會強制用戶端使用 SSL 連線。 此 `.pem` 憑證會用來與適用於 MySQL 的 Azure 資料庫進行 SSL 連線。 如需詳細資訊，請參閱[在您的應用程式中設定 SSL 連線能力，以安全地連線至適用於 MySQL 的 Azure 資料庫](../../mysql/howto-configure-ssl.md)。
>

### <a name="test-the-application-locally"></a>在本機測試應用程式

在本機終端機中，設定下列環境變數：

```bash
export DB_HOST=<mysql_server_name>.mysql.database.azure.com
export DB_DATABASE=sampledb 
export DB_USERNAME=railsappuser@<mysql_server_name>
export DB_PASSWORD=MySQLAzure2017
```

使用您剛剛設定的生產值來執行 Rails 資料庫移轉，可在適用於 MySQL 的 Azure 資料庫 (預覽) 中建立資料表。 

```bash
rake db:migrate RAILS_ENV=production
```

當 Rails 應用程式在生產環境中執行時，其需要預先編譯的資產。 請使用下列命令產生必要資產：

```bash
rake assets:precompile
```

Rails 生產環境也會使用祕密來管理安全性。 產生祕密金鑰。

```bash
rails secret
```

將祕密金鑰儲存至 Rails 生產環境所使用的個別變數。 為了方便起見，您可對兩個變數使用相同的金鑰。

```bash
export RAILS_MASTER_KEY=<output_of_rails_secret>
export SECRET_KEY_BASE=<output_of_rails_secret>
```

讓 Rails 生產環境可支援 JavaScript 和 CSS 檔案。

```bash
export RAILS_SERVE_STATIC_FILES=true
```

在生產環境中執行範例應用程式。

```bash
rails server -e production
```

瀏覽至 `http://localhost:3000`。 如果頁面載入無誤，Ruby on Rails 應用程式就會連線至 Azure 中的 MySQL 資料庫。

在頁面中新增幾項工作。

![Ruby on Rails 順利連線至適用於 MySQL 的 Azure 資料庫 (預覽)](./media/tutorial-ruby-mysql-app/azure-mysql-connect-success.png)

若要停止 Rails 伺服器，請在終端機中輸入 `Ctrl + C`。

### <a name="commit-your-changes"></a>認可變更

執行下列的 Git 命令認可您的變更：

```bash
git add .
git commit -m "database.yml updates"
```

您的應用程式已準備好進行部署。

## <a name="deploy-to-azure"></a>部署至 Azure

在此步驟中，您會將與 MySQL 連線的 Rails 應用程式部署至 Azure App Service。

### <a name="configure-a-deployment-user"></a>設定部署使用者

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>建立應用程式服務方案

[!INCLUDE [Create app service plan no h](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>建立 Web 應用程式

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-ruby-linux-no-h.md)] 

### <a name="configure-database-settings"></a>設定資料庫設定

在 App Service 中，您可以在 Cloud Shell 中使用 [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) 命令將環境變數設定為「應用程式設定」。

下列 Cloud Shell 命令會設定 `DB_HOST`、`DB_DATABASE`、`DB_USERNAME`和 `DB_PASSWORD` 應用程式設定。 取代預留位置 _&lt;appname>_ 和 _&lt;mysql_server_name>_。

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DB_HOST="<mysql_server_name>.mysql.database.azure.com" DB_DATABASE="sampledb" DB_USERNAME="railsappuser@<mysql_server_name>" DB_PASSWORD="MySQLAzure2017"
```

### <a name="configure-rails-environment-variables"></a>設定 Rails 環境變數

在本機終端機中，為 Azure 中的 Rails 生產環境產生新的秘密金鑰。

```bash
rails secret
```

設定 Rails 生產環境所需的變數。

在下列 Cloud Shell 命令中，以本機終端機中產生的新秘密金鑰取代兩個 _&lt;output_of_rails_secret>_ 預留位置。

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings RAILS_MASTER_KEY="<output_of_rails_secret>" SECRET_KEY_BASE="<output_of_rails_secret>" RAILS_SERVE_STATIC_FILES="true" ASSETS_PRECOMPILE="true"
```

`ASSETS_PRECOMPILE="true"` 會告知預設的 Ruby 容器要預先編譯各個 Git 部署上的資產。

### <a name="push-to-azure-from-git"></a>從 Git 推送至 Azure

在本機終端中，將 Azure 遠端新增至本機 Git 存放庫。

```bash
git remote add azure <paste_copied_url_here>
```

推送至 Azure 遠端以部署 Ruby on Rails 應用程式。 建立部署使用者時，系統會提示您輸入稍早提供的密碼。

```bash
git push azure master
```

在部署期間，Azure App Service 會與 Git 溝通其進度。

```bash
Counting objects: 3, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 291 bytes | 0 bytes/s, done.
Total 3 (delta 2), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'a5e076db9c'.
remote: Running custom deployment command...
remote: Running deployment command...
...
< Output has been truncated for readability >
```

### <a name="browse-to-the-azure-web-app"></a>瀏覽至 Azure Web 應用程式

瀏覽至 `http://<app_name>.azurewebsites.net` 並將幾項工作新增至清單。

![在 Azure App Service 中執行的 Ruby on Rails 應用程式](./media/tutorial-ruby-mysql-app/ruby-mysql-in-azure.png)

恭喜，您正在 Azure App Service 中執行資料驅動的 Ruby on Rails 應用程式。

## <a name="update-model-locally-and-redeploy"></a>在本機更新模型並重新部署

在此步驟中，您會簡單變更 `task` 資料模型和 webapp，然後將更新發行至 Azure。

在此工作案例中，您將修改應用程式，讓您可以將工作標示為完成。

### <a name="add-a-column"></a>新增資料行

在終端機中，瀏覽至 Git 存放庫的根目錄。

產生新的移轉，以將名為 `Done` 的布林資料行新增至 `Tasks` 資料表：

```bash
rails generate migration add_Done_to_Tasks Done:boolean
```

此命令會在 db/migrate 目錄中產生新的移轉檔案。


在終端機中，執行 Rails 資料庫移轉，以在本機資料庫中進行變更。

```bash
rake db:migrate
```

### <a name="update-application-logic"></a>更新應用程式邏輯

開啟 app/controllers/tasks_controller.rb 檔案。 在檔案結尾處，尋找下列這一行︰

```rb
params.require(:task).permit(:Description)
```

修改此行，以包含新的 `Done` 參數。

```rb
params.require(:task).permit(:Description, :Done)
```

### <a name="update-the-views"></a>更新檢視

開啟 app/views/tasks/_form.html.erb 檔案，這是編輯表單。

找到 `<%=f.error_span(:Description) %>` 行，並直接將下列程式碼插入該行下方：

```erb
<%= f.label :Done, :class => 'control-label col-lg-2' %>
<div class="col-lg-10">
  <%= f.check_box :Done, :class => 'form-control' %>
</div>
```

開啟 *app/views/tasks/show.html.erb* 檔案，這是單一記錄的檢視頁面。 

找到 `<dd><%= @task.Description %></dd>` 行，並直接將下列程式碼插入該行下方：

```erb
  <dt><strong><%= model_class.human_attribute_name(:Done) %>:</strong></dt>
  <dd><%= check_box "task", "Done", {:checked => @task.Done, :disabled => true}%></dd>
```

開啟 app/views/tasks/index.html.erb 檔案，這是所有記錄的索引頁面。

找到 `<th><%= model_class.human_attribute_name(:Description) %></th>` 行，並直接將下列程式碼插入該行下方：

```erb
<th><%= model_class.human_attribute_name(:Done) %></th>
```

在相同檔案中，找到 `<td><%= task.Description %></td>` 行，並直接將下列程式碼插入該行下方：

```erb
<td><%= check_box "task", "Done", {:checked => task.Done, :disabled => true} %></td>
```

### <a name="test-the-changes-locally"></a>本機測試變更

在本機終端機中，執行 Rails 伺服器。

```bash
rails server
```

若要查看工作狀態的變更，請瀏覽至 `http://localhost:3000` 並新增或編輯項目。

![已將核取方塊新增至工作](./media/tutorial-ruby-mysql-app/complete-checkbox.png)

若要停止 Rails 伺服器，請在終端機中輸入 `Ctrl + C`。

### <a name="publish-changes-to-azure"></a>將變更發佈至 Azure

在終端機中，針對生產環境執行 Rails 資料庫移轉，以在 Azure 資料庫中進行變更。

```bash
rake db:migrate RAILS_ENV=production
```

在 Git 中認可所有變更，然後將程式碼變更推送至 Azure。

```bash
git add .
git commit -m "added complete checkbox"
git push azure master
```

完成 `git push` 之後，瀏覽至 Azure Web 應用程式，然後測試新功能。

![發佈至 Azure 的模型和資料庫變更](media/tutorial-ruby-mysql-app/complete-checkbox-published.png)

如果您新增任何工作，它們會保留在資料庫中。 對資料結構描述進行的更新，現有資料會原封不動。

## <a name="manage-the-azure-web-app"></a>管理 Azure Web 應用程式

請移至 [Azure 入口網站](https://portal.azure.com)，以管理您所建立的 Web 應用程式。

按一下左側功能表中的 [應用程式服務]，然後按一下 Azure Web 應用程式的名稱。

![入口網站瀏覽至 Azure Web 應用程式](./media/tutorial-php-mysql-app/access-portal.png)

您會看到 Web 應用程式的 [概觀] 頁面。 您可以在這裡執行基本管理工作，像是停止、啟動、重新啟動、瀏覽、刪除。

左側功能表提供的頁面可用來設定您的應用程式。

![Azure 入口網站中的 App Service 頁面](./media/tutorial-php-mysql-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

<a name="next"></a>

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 在 Azure 中建立 MySQL 資料庫
> * 將 Ruby on Rails 應用程式連線到 MySQL
> * 將應用程式部署至 Azure
> * 將資料模型更新並將應用程式重新部署
> * 來自 Azure 的串流診斷記錄
> * 在 Azure 入口網站中管理應用程式

前往下一個教學課程，了解如何將自訂的 DNS 名稱對應至 Web 應用程式。

> [!div class="nextstepaction"]
> [將現有的自訂 DNS 名稱對應至 Azure Web Apps](../app-service-web-tutorial-custom-domain.md)
