<properties 
	pageTitle="開發以 ASP.NET MVC 使用 DocumentDB 的 Web 應用程式 | Microsoft Azure" 
	description="了解如何使用 DocumentDB with .NET 建立待辦事項清單 Web 應用程式。您將從 Azure 網站託管的 ASP.NET MVC Web 應用程式中儲存和存取資料。" 
	keywords="Github、visual studio、web 應用程式開發、應用程式開發、資料庫教學課程、mvc 應用程式、json 資料、documentdb、azure、Microsoft azure"
	services="documentdb" 
	documentationCenter=".net" 
	authors="ryancrawcour" 
	manager="jhubbard" 
	editor="cgronlun"/>


<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="hero-article" 
	ms.date="07/24/2015" 
	ms.author="ryancraw"/>

#<a name="_Toc395809351"></a>使用 DocumentDB 搭配 ASP.NET MVC 的 Web 應用程式開發

> [AZURE.SELECTOR]
- [.NET](documentdb-dotnet-application.md)
- [Node.js](documentdb-nodejs-application.md)
- [Java](documentdb-java-application.md)
- [Python](documentdb-python-application.md) 

為了特別說明您可以如何有效率地利用 Azure DocumentDB 來儲存和查詢 JSON 文件，本文提供如何使用 Azure DocumentDB 建置待辦事項清單 Web 應用程式的完整逐步解說。在 Azure DocumentDB 中，這些工作將會儲存為 JSON 文件。

![本教學課程所建立的待辦事項清單 Web 應用程式的螢幕擷取畫面](./media/documentdb-dotnet-application/image1.png)

本逐步解說說明如何使用 Azure 所提供的 DocumentDB 服務，來儲存和存取 Azure 上所託管 ASP.NET MVC Web 應用程式的資料。

> [AZURE.TIP]本教學課程假設您先前有過使用 ASP.NET MVC 和 Azure 網站的經驗。如果您不熟悉 ASP.NET 或[必備工具](#_Toc395637760)，我們建議您從 [GitHub][] 下載完整的範例專案，並依照此範例的指示進行。建置完成後，您可以檢閱此文章，以加深對專案內容中程式碼的了解。

## <a name="_Toc395637760"></a>此資料庫教學課程的必要條件

在依照本文中的指示進行之前，您應先確定備妥下列項目：

- 使用中的 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用](../../pricing/free-trial/)。
- [Visual Studio 2013](http://www.visualstudio.com/) Update 4 或更新版本。
- Azure SDK for .NET 2.5.1 版或更高版本，可透過 [Microsoft Web Platform Installer][] (英文) 取得。

本文中的所有螢幕擷取畫面都是使用已套用 Update 4 的 Visual Studio 2013 以及 Azure SDK for .NET 2.5.1 版所取得。如果您的系統是設定使用不同的版本，則您的畫面和選項可能不會完全相符，但只要您符合上述必要條件，本方案應該還是有效。

## <a name="_Toc395637761"></a>步驟 1：建立 DocumentDB 資料庫帳戶

現在就開始建立 DocumentDB 帳戶。如果您已經有帳戶，您可以跳到[建立新的 ASP.NET MVC 應用程式](#_Toc395637762)。

[AZURE.INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

[AZURE.INCLUDE [documentdb-keys](../../includes/documentdb-keys.md)]

<br/>我們現在將從頭開始逐步解說如何建立新的 ASP.NET MVC 應用程式。

## <a name="_Toc395637762"></a>步驟 2：建立新的 ASP.NET MVC 應用程式

既然您已有帳戶，我們可以開始建立新的 ASP.NET 專案。

1. 在 Visual Studio 的 [檔案] 功能表中，指向 [新增]，然後按一下 [專案]。

   	[新增專案] 對話方塊隨即出現。
2. 在 [專案類型] 窗格中，依序展開 [範本]、[Visual C#]、[Web]，然後選取 [ASP.NET Web 應用程式]。

  	![[新增專案] 對話方塊的螢幕擷取畫面，內含反白顯示的 ASP.NET Web 應用程式專案類型](./media/documentdb-dotnet-application/image10.png)

3. 在 [名稱] 方塊中，輸入專案的名稱。本教學課程使用 "todo" 名稱。如果您選擇使用其他名稱，則每當本教學課程提及 todo 命名空間時，您必須調整提供的程式碼範例，以便使用您為應用程式所命名的名稱。

4. 按一下 [瀏覽] 以巡覽至您想要建立專案的資料夾，然後按一下 [確定]。

  	[新增 ASP.NET 專案] 對話方塊隨即出現。

  	![[新增 ASP.NET 專案] 對話方塊的螢幕擷取畫面，內含反白顯示的 MVC 應用程式範本，和勾選的 [在雲端託管] 方塊。](./media/documentdb-dotnet-application/image11.png)

5. 在 [範本] 窗格中，選取 [MVC]。

6. 如果您打算在 Azure 中託管應用程式，則請選取右下角的 [在雲端託管] 方塊，Azure 才能託管應用程式。我們已選擇要在雲端託管，並執行 Azure 網站中所託管的應用程式。選取此選項將會預先佈建 Azure 網站，讓您在需要部署最終的運作應用程式時更為容易。如果您希望在各處都能托管，或不想預先設定，則請清除 [在雲端託管] 選項。

7. 按一下 [確定]，並讓 Visual Studio 執行有關 Scaffolding 空的 ASP.NET MVC 範本的作業。

8. 如果您選擇在雲端託管，則至少會有一個其他畫面出現，要求您登入 Azure 帳戶並提供新網站的部分值。提供所有其他的值，然後繼續進行。

  	在這裡我並沒有選擇 [資料庫伺服器]，因為我們並未使用 Azure SQL Database Server，稍後我們會在 Azure Preview 入口網站中建立新的 Azure DocumentDB 帳戶。

	如需選擇 **App Service 方案**和**資源群組**的詳細資訊，請參閱 [Azure App Service 方案深入概觀](azure-web-sites-web-hosting-plans-in-depth-overview.md)。

  	![[設定 Microsoft Azure 網站] 對話方塊的螢幕擷取畫面](./media/documentdb-dotnet-application/image11_1.png)

9. Visual Studio 建立好未定案 MVC 應用程式之後，您便擁有可以在本機執行的空白 ASP.NET 應用程式。

	我們會跳過在本機執行專案，因為我確定我們都已看過 ASP.NET "Hello World" 應用程式。讓我們直接跳到將 DocumentDB 新增至此專案並建置應用程式的步驟。

## <a name="_Toc395637767"></a>步驟 3：將 DocumentDB 新增至 Web 應用程式專案

既然我們已經完成了此方案的大部分 ASP.NET MVC 瑣事，我們現在可以開始本教學課程的真正目的，也就是將 Azure DocumentDB 新增至 Web 應用程式。

1. DocumentDB .NET SDK 會隨 NuGet 封裝而分散。若要在 Visual Studio 中取得 NuGet 封裝，請使用 Visual Studio 中的 NuGet 封裝管理員，方法是以滑鼠右鍵按一下 [方案總管] 中的專案，然後按一下 [管理 NuGet 封裝]。

  	![[方案總管] 中 Web 應用程式專案的滑鼠右鍵選項的螢幕擷取畫面，內含反白顯示的 [管理 NuGet 封裝]。](./media/documentdb-dotnet-application/image21.png)

    [管理 NuGet 封裝] 對話方塊隨即出現。

2. 在 [線上搜尋] 方塊中，輸入 ***Azure DocumentDB***。
    
    從結果中，安裝 [Microsoft Azure DocumentDB 用戶端程式庫] 封裝。這會下載和安裝 DocumentDB 封裝，以及所有依存項目 (例如 Newtonsoft.Json)。

  	![[管理 NuGet 封裝] 視窗的螢幕擷取畫面，內含反白顯示的 Microsoft Azure DocumentDB 用戶端程式庫](./media/documentdb-dotnet-application/nuget.png)

  	或者，您也可以使用 Package Manager Console 來安裝封裝。若要這樣做，請在 [工具] 功能表上按一下 [NuGet 封裝管理員]，然後按一下 [Package Manager Console]。在出現提示時輸入下列內容：

    	Install-Package Microsoft.Azure.DocumentDB

3. 安裝封裝之後，您的 Visual Studio 方案應該類似下列已新增兩個新參考 (Microsoft.Azure.Documents.Client 和 Newtonsoft.Json) 的方案。

  	![[方案總管] 中 JSON 資料專案新增兩個參考的螢幕擷取畫面](./media/documentdb-dotnet-application/image22.png)


##<a name="_Toc395637763"></a>步驟 4：設定 ASP.NET MVC 應用程式
 
現在我們可以開始將模型、檢視和控制站新增至此 MVC 應用程式：

- [新增模型](#_Toc395637764)。
- [新增控制器](#_Toc395637765)。
- [新增檢視](#_Toc395637766)。


### <a name="_Toc395637764"></a>新增 JSON 資料模型

首先，讓我們在 MVC 中建立 **M** (模型)。

1. 在 [方案總管] 中，以滑鼠右鍵按一下 **Models** 資料夾，再按一下 [新增]，然後按一下 [類別]。

  	[加入新項目] 對話方塊隨即出現。

2. 將您的新類別命名為 **Item.cs**，然後按一下 [新增]。

3. 在這個新的 **Item.cs** 檔案中，將下列程式碼加到最後一個 *using 陳述式* 後面。
		
		using Newtonsoft.Json;
	
4. 現在，將此程式碼取代
		
		public class Item
		{
		}

	為下列程式碼。
		
        public class Item
        {
        	[JsonProperty(PropertyName="id")]
        	public string Id { get; set; }
		
        	[JsonProperty(PropertyName="name")]
        	public string Name { get; set; }
		
        	[JsonProperty(PropertyName = "desc")]
        	public string Description { get; set; }
		
       		[JsonProperty(PropertyName="isComplete")]
        	public bool Completed { get; set; }    
		}

	DocumentDB 中的所有資料都會透過線路傳遞，並儲存為 JSON。如需透過 JSON.NET 控管物件序列化/取消序列化，您可以使用在剛才建立的 [項目] 類別中所示範 **JsonProperty** 屬性。您**無需**這樣做，但我想確定所有屬性都會依照 JSON camelCase 的命名慣例命名。
	
	使用 JSON 時，您不但可以控管屬性名稱格式，也可以跟我命名 **Description** 屬性一樣重新命名您的 .NET 屬性。
	

### <a name="_Toc395637765"></a>新增控制器

我們已經建立了 **M**，現在讓我們建立 MVC 中的 **C** (控制器類別)。

1. 在 [方案總管] 中，以滑鼠右鍵按一下 **Controllers** 資料夾，按一下 [新增]，然後按一下 [控制器]。

    [新增 Scaffold] 對話方塊隨即出現。

2. 選取 [Web API 5 控制器 - 空]，然後按一下 [新增]。

	![[新增 Scaffold] 對話方塊的螢幕擷取畫面，內含反白顯示的 [MVC 5 控制器 - 空的] 選項](./media/documentdb-dotnet-application/image14.png)

3. 將新的控制器命名為 **ItemController**。

	![[新增控制器] 對話方塊的螢幕擷取畫面](./media/documentdb-dotnet-application/image15.png)

	檔案建立之後，您的 Visual Studio 方案應該類似包含下面 [方案總管] 中新 ItemController.cs 檔案的方案。稍早建立的新 Item.cs 檔案也會顯示出來。

	![Visual Studio 的螢幕擷取畫面，[方案總管] 內含反白顯示的新 ItemController.cs 檔案和 Item.cs 檔案](./media/documentdb-dotnet-application/image16.png)

	您可以先將 ItemController.cs 關閉，我們稍後會回頭使用此檔案。

### <a name="_Toc395637766"></a>新增檢視

現在，我們可以開始建立 MVC 中的 **V** (檢視)：

- [新增項目索引檢視](#AddItemIndexView)。
- [新增項目檢視](#AddNewIndexView)。
- [新增編輯項目檢視](#_Toc395888515)。


#### <a name="AddItemIndexView"></a>新增項目索引檢視

1. 在 [方案總管] 中，展開 **Views** 資料夾，以滑鼠右鍵按一下稍早您在建立 **ItemController** 時，Visual Studio 為您建立的空白 **Item** 資料夾，按一下 [新增]，然後按一下 [檢視]。

	![顯示 Visual Studio 方案建立之 Item 資料夾的 [方案總管] 螢幕擷取畫面，內含反白顯示的 [新增檢視] 命令](./media/documentdb-dotnet-application/image17.png)

2. 在 [新增檢視] 對話方塊中，執行下列動作：
	- 在 [檢視名稱] 方塊中，輸入「***索引***」。
	- 在 [範本] 方塊中，選取 [清單]。
	- 在 [模型類別] 方塊中，選取 [項目 (todo.Models)]。
	- 將 [資料內容類別] 方塊保留空白。 
	- 在 [版面配置頁面] 方塊中，輸入 ***~/Views/Shared/\_Layout.cshtml***。
	
	![顯示 [新增檢視] 對話方塊的螢幕擷取畫面](./media/documentdb-dotnet-application/image18.png)

3. 設定所有這些值之後，按一下 [新增]，並讓 Visual Studio 建立新的範本檢視。完成之後，系統便會開啟剛建立的 cshtml 檔案。我們稍後會回頭使用此檔案，您可以先在 Visual Studio 中將該檔案關閉。

#### <a name="AddNewIndexView"></a>新增項目檢視

與建立 [項目索引] 檢視的方式類似，現在我們現在可以開始建立新的檢視，以供建立新的**項目**使用。

1. 在 [方案總管] 中，再次以滑鼠右鍵按一下 **Item** 資料夾，按一下 [新增]，然後按一下 [檢視]。

2. 在 [**新增檢視]** 對話方塊中，執行下列動作：
    - 在 [檢視名稱] 方塊中，輸入「***建立***」。
    - 在 [**範本**] 方塊中，選取 [***建立***]。
    - 在 [模型類別] 方塊中，選取 [項目 (todo.Models)]。
    - 將 [資料內容類別] 方塊保留空白。
    - 在 [版面配置頁面] 方塊中，輸入 ***~/Views/Shared/\_Layout.cshtml***。
    - 按一下 [新增]。

#### <a name="_Toc395888515"></a>新增編輯項目檢視

最後，使用與之前相同的方式新增最後一個檢視，以供編輯**項目**使用；

1. 在 [方案總管] 中，再次以滑鼠右鍵按一下 **Item** 資料夾，按一下 [新增]，然後按一下 [檢視]。

2. 在 [新增檢視] 對話方塊中，執行下列動作：
    - 在 [檢視名稱] 方塊中，輸入「***編輯***」。
    - 在 [範本] 方塊中，選取 [編輯]。
    - 在 [模型類別] 方塊中，選取 [項目 (todo.Models)]。
    - 將 [資料內容類別] 方塊保留空白。 
    - 在 [版面配置頁面] 方塊中，輸入 ***~/Views/Shared/\_Layout.cshtml***。
    - 按一下 [新增]。

完成這項作業之後，請將 Visual Studio 中的所有 cshtml 文件關閉，我們稍後會回頭使用這些檢視。

## <a name="_Toc395637769"></a>步驟 5：組建 DocumentDB

我們已經建立了標準的 MVC 項目，現在我們可以開始新增 DocumentDB 的程式碼。

在本節中，我們將新增程式碼來處理下列作業：

- [列出未完成項目](#_Toc395637770)。
- [新增項目](#_Toc395637771)。
- [編輯項目](#_Toc395637772)。

### <a name="_Toc395637770"></a>列出 Web 應用程式中的未完成項目

首先要執行的作業是新增類別，其中包含連線至及使用 DocumentDB 的所有邏輯。在本教學課程中，我們會將所有邏輯封裝到名為 DocumentDBRepository 的儲存機制類別中。

1. 在 [方案總管] 中，以滑鼠右鍵按一下專案，按一下 [新增]，然後按一下 [類別]。將新類別命名為 **DocumentDBRepository**，然後按一下 [新增]。
 
2. 在剛剛建立的 **DocumentDBRepository** 類別中，在*命名空間*宣告上方新增下列 *using 陳述式*
		
		using Microsoft.Azure.Documents; 
	    using Microsoft.Azure.Documents.Client; 
	    using Microsoft.Azure.Documents.Linq; 
		using System.Configuration;
		using System.Linq.Expressions;
		using System.Threading.Tasks;

	現在，將此程式碼取代

		public class DocumentDBRepository
		{
    	}

	為下列程式碼。

    	
		public static class DocumentDBRepository<T>
    	{
			//Use the Database if it exists, if not create a new Database
	    	private static Database ReadOrCreateDatabase()
	    	{
	        	var db = Client.CreateDatabaseQuery()
	        	                .Where(d => d.Id == DatabaseId)
	        	                .AsEnumerable()
	        	                .FirstOrDefault();
				
	        	if (db == null)
	        	{
	        	    db = Client.CreateDatabaseAsync(new Database { Id = DatabaseId }).Result;
	        	}
				
	        	return db;
	    	}
			
			//Use the DocumentCollection if it exists, if not create a new Collection
	    	private static DocumentCollection ReadOrCreateCollection(string databaseLink)
	   		{
	    	    var col = Client.CreateDocumentCollectionQuery(databaseLink)
	        	                  .Where(c => c.Id == CollectionId)
	        	                  .AsEnumerable()
	        	                  .FirstOrDefault();
		
	        	if (col == null)
	        	{
					var collectionSpec = new DocumentCollection { Id = CollectionId };
					var requestOptions = new RequestOptions {OfferType = "S1" };
					
	        	    col = Client.CreateDocumentCollectionAsync(databaseLink, collectionSpec, requestOptions).Result;
	        	}
				
	        	return col;
	    	}
			
			//Expose the "database" value from configuration as a property for internal use
     	   	private static string databaseId;
     	   	private static String DatabaseId
     	   	{
				get
				{
					if (string.IsNullOrEmpty(databaseId))
					{
						databaseId = ConfigurationManager.AppSettings["database"];
					}
				
					return databaseId;
				}
       	 	}
			
			//Expose the "collection" value from configuration as a property for internal use
    	    private static string collectionId;
    	    private static String CollectionId
    	    {
				get
				{
					if (string.IsNullOrEmpty(collectionId))
					{
						collectionId = ConfigurationManager.AppSettings["collection"];
					}
				
					return collectionId;
				}
    	    }
			
			//Use the ReadOrCreateDatabase function to get a reference to the database.
    	    private static Database database;
    	    private static Database Database
    	    {
				get
				{
					if (database == null)
					{
						database = ReadOrCreateDatabase();
					}
					
					return database;
				}
    	    }
			
			//Use the ReadOrCreateCollection function to get a reference to the collection.
    	    private static DocumentCollection collection;
    	    private static DocumentCollection Collection
    	    {
				get
				{
					if (collection == null)
					{
						collection = ReadOrCreateCollection(Database.SelfLink);
					}
					
					return collection;
				}
    	    }
			
			//This property establishes a new connection to DocumentDB the first time it is used, 
			//and then reuses this instance for the duration of the application avoiding the
			//overhead of instantiating a new instance of DocumentClient with each request
    	    private static DocumentClient client;
    	    private static DocumentClient Client
    	    {
    	        get
    	        {
    	            if (client == null)
    	            {
						string endpoint = ConfigurationManager.AppSettings["endpoint"];
						string authKey = ConfigurationManager.AppSettings["authKey"];
						Uri endpointUri = new Uri(endpoint);
						client = new DocumentClient(endpointUri, authKey);
    	            }
    	            
    	            return client;
    	        }
    	    }
    	}

	> [AZURE.TIP]建立新的 DocumentCollection 時，您可以提供 OfferType 的選擇性 RequestOptions 參數，此參數可讓您指定新集合的效能層級。如果無法傳遞此參數，系統將會使用預設的優惠類型。如需 DocumentDB 優惠類型的詳細資訊，請參閱 [DocumentDB 效能層級](documentdb-performance-levels.md)

3. 我們打算從組態中讀取部分值，因此請開啟應用程式的 **Web.config** 檔案，並在 [`<AppSettings>`] 區段下新增下列幾行。
	
    	<add key="endpoint" value="enter the URI from the Keys blade of the Azure Preview portal"/>
    	<add key="authKey" value="enter the PRIMARY KEY, or the SECONDARY KEY, from the Keys blade of the Azure  Preview portal"/>
    	<add key="database" value="ToDoList"/>
    	<add key="collection" value="Items"/>
	
4. 現在，使用Azure Preview 入口網站的 [金鑰] 刀鋒視窗來更新 [端點] 和 [authKey] 的值。使用 [金鑰] 刀鋒視窗的 [URI] 做為端點設定的值，並使用 [金鑰] 刀鋒視窗的 [主索引鍵] 或 [次要金鑰] 做為 authKey 設定的值。


    負責裝設 DocumentDB 儲存機制，現在讓我們加入我們的應用程式邏輯。

5. 我們想要對 todo 清單應用程式進行的第一件事就是顯示未完成的項目。在 **DocumentDBRepository** 類別內的任意位置複製並貼上下列程式碼片段。

	    public static IEnumerable<T> GetItems(Expression<Func<T, bool>> predicate) 
	    {
			return Client.CreateDocumentQuery<T>(Collection.DocumentsLink) 
				.Where(predicate) 
				.AsEnumerable(); 
		} 

6. 開啟我們剛剛新增的 **ItemController**，並在命名空間宣告上方新增下列 *using 陳述式*

		using System.Net;
		using System.Threading.Tasks;
		using todo.Models;

	如果您的專案名稱不是 "todo"，則您必須使用 "todo.Models" 進行更新，才能反映您的專案名稱。

	現在，將此程式碼取代

		//GET: Item
		public ActionResult Index()
		{
			return View();
		}

	為下列程式碼。

    	public ActionResult Index()
    	{
			var items = DocumentDBRepository<Item>.GetItems(d => !d.Completed);
			return View(items);
    	}
	
此時，應該已經可以建置方案，而不會發生任何錯誤。

如果您現在執行應用程式，您可以前往 **HomeController** 及該控制器的 [索引] 檢視。這是我們在一開始時所選擇的 MVC 範本專案預設行為，但是我們不想要這樣的行為！ 讓我們變更此 MVC 應用程式上的路由以改變此行為。

開啟 ***App\_Start\\RouteConfig.cs***，並尋找以 "defaults:" 開頭的程式碼行，然後將它變更為如下所示。

    	defaults: new { controller = "Item", action = "Index", id = UrlParameter.Optional }

如果您未在 URL 中指定控制路由行為的值，這會讓 ASP.NET MVC 知道改用 **Item** (**Home**) 作為控制器，並使用使用者**索引**作為檢視。

如果您執行應用程式，它現在會呼叫至您的 **ItemController**，進而呼叫至儲存機制類別，並使用 GetItems 方法將所有未完成的項目傳回 **Views**\**Item**\**Index** 檢視。

如果建置並立即執行此專案，您現在應該會看到如下的內容。

![本資料庫教學課程所建立的待辦事項清單 Web 應用程式的螢幕擷取畫面](./media/documentdb-dotnet-application/image23.png)

### <a name="_Toc395637771"></a>新增項目

我們可以開始將一些項目放入資料庫中，所以除了空白方格以外，我們還可以看到其他項目。

讓我們將一些程式碼新增至 DocumentDBRepository 和 ItemController，以在 DocumentDB 中保留記錄。

1.  將下列方法新增至 **DocumentDBRepository** 類別。

    	public static async Task<Document> CreateItemAsync(T item)
   	 	{
   	   		return await Client.CreateDocumentAsync(Collection.SelfLink, item);
   		}

	此方法只會接受傳遞給它的物件，並將它保留在 DocumentDB 中。

2. 開啟 ItemController.cs 檔案，並在類別中加入下列程式碼片段。這是 ASP.NET MVC 得知如何執行**建立**動作的方式。在此情況下，只需轉譯先前建立的關聯 Create.cshtml 檢視。

    	public ActionResult Create()
    	{ 
			return View(); 
   		}

	現在此控制器需要更多程式碼，以接受 [建立] 檢視所提交的資料。

2. 將下一個程式碼區塊新增至 ItemController.cs 類別，以告訴 ASP.NET MVC 如何使用表單 POST 來執行此控制器的作業。
	
    	[HttpPost]
    	[ValidateAntiForgeryToken]
   	 	public async Task<ActionResult> Create([Bind(Include = 	"Id,Name,Description,Completed")] Item item)  
  	  	{
			if (ModelState.IsValid)  
			{  
			    await DocumentDBRepository<Item>.CreateItemAsync(item);
			    return RedirectToAction("Index");  
			}   
			return View(item);   
		}
	這段程式碼會呼叫 DocumentDBRepository，並且使用 CreateItemAsync 方法將新的待辦事項項目保存到資料庫。
 
	**安全性注意事項**：此處所使用的 **ValidateAntiForgeryToken** 屬性可協助應用程式防止跨網站偽造要求攻擊。這不光只是新增此屬性，您的檢視也必須使用這個防偽權杖。如需此主題的詳細資訊以及如何正確實作此作業的範例，請參閱[防止跨網站偽造要求][]。[GitHub][] 上提供的原始程式碼已有完整實作。

	**安全性注意事項**：我們也會在方法參數上使用 **Bind** 屬性，以協助防範 over-posting 攻擊。如需詳細資訊，請參閱 [ASP.NET MVC 中的基本 CRUD 作業][]。

這包括將新項目新增至資料庫所需的程式碼。


### <a name="_Toc395637772"></a>編輯項目

我們最後還要做一件事，那就是新增在資料庫中編輯**項目**，以及將它們標示為已完成的功能。編輯的檢視已經新增至專案，因此，我們只需重新將某些程式碼新增至控制器及 **DocumentDBRepository** 類別即可。

1. 將下列程式碼新增至 **DocumentDBRepository** 類別。

    	public static T GetItem(Expression<Func<T, bool>> predicate)
    	{
        	return Client.CreateDocumentQuery<T>(Collection.DocumentsLink)
                    	.Where(predicate)
                    	.AsEnumerable()
                    	.FirstOrDefault();
    	}		

    	public static async Task<Document> UpdateItemAsync(string id, T item)
    	{
        	Document doc = GetDocument(id);	
        	return await Client.ReplaceDocumentAsync(doc.SelfLink, item);
    	}

		private static Document GetDocument(string id) 
		{ 
			return Client.CreateDocumentQuery(Collection.DocumentsLink) 
				.Where(d => d.Id == id) 
				.AsEnumerable() 
				.FirstOrDefault(); 
		} 
	
	這兩個方法中的第一個方法 (**GetItem**) 會從 DocumentDB 提取項目，此項目會被傳回 **ItemController**，接著傳至 [編輯] 檢視。
	
	第二個剛剛新增的方法是使用從 **ItemController** 傳入的**文件**版本，來取代 DocumentDB 中的**文件**。

2. 將下列程式碼新增至 **ItemController** 類別。

		public ActionResult Edit(string id)
		{
		    if (string.IsNullOrEmpty(id))
		    {
		        return new HttpStatusCodeResult(HttpStatusCode.BadRequest);
		    }
					 
		    Item item = (Item)DocumentDBRepository<Item>.GetItem(d => d.Id == id);
		 	
		    if (item == null)
		    {
		        return HttpNotFound();
		    }
		 	
		    return View(item);
		}
		
    	[HttpPost]
   		[ValidateAntiForgeryToken]
    	public async Task<ActionResult> Edit([Bind(Include = "Id,Name,Description,Completed")] Item item)
    	{
     	   if (ModelState.IsValid)
    	    {
    	        await DocumentDBRepository<Item>.UpdateItemAsync(item.Id, item);
    	        return RedirectToAction("Index");
    	    }

  	      return View(item);
 	   	}
		
	
	第一個方法會處理當使用者按一下 [索引] 檢視中的 [編輯] 連結時所發生的 Http GET。此方法會從 DocumentDB 中提取 [**Document**](http://msdn.microsoft.com/library/azure/microsoft.azure.documents.document.aspx)，並將它傳遞給 [編輯] 檢視。

	[編輯] 檢視會接著對 **IndexController** 執行 Http POST。
	
	新增的第二個方法會處理此作業，將更新物件傳遞至 DocumentDB 並保留在資料庫中。

這樣便大功告成了，這些就是我們必須執行應用程式的所有作業：列出未完成**項目**、新增**項目**，最後是編輯**項目**。

## <a name="_Toc395637773"></a>步驟 6：在本機執行應用程式

若要在本機電腦測試應用程式，請執行下列動作：

1. 在 Visual Studio 中按 F5，即可在偵錯模式下建置應用程式。這樣應該可以建置應用程式，並啟動含有先前所看過之空白方格頁面的瀏覽器：

	![本資料庫教學課程所建立的待辦事項清單 Web 應用程式的螢幕擷取畫面](./media/documentdb-dotnet-application/image24.png)

	如果這個時候發生錯誤，您可以將程式碼與 [GitHub][] 上的範例專案進行比對

2. 按一下 [新建] 連結，並在 [名稱] 和 [描述] 欄位中新增值。將 [已完成] 核取方塊保持為未選取狀態，否則，**新項目**會以已完成的狀態新增，且不會出現在初始清單中。

	![[建立] 檢視的螢幕擷取畫面](./media/documentdb-dotnet-application/image25.png)

3. 按一下 [建立]，您便會被重新導向回到 [索引] 檢視，且您的**項目**便會出現在清單中。

	![[索引] 檢視的螢幕擷取畫面](./media/documentdb-dotnet-application/image26.png)

	依需要新增一些**項目**至 [待辦事項] 清單。

3. 按一下清單上 [項目] 旁邊的 [編輯]，您便會被帶到 [編輯] 檢視，您可以在此更新物件的任何屬性 (包括 [已完成] 旗標)。如果您標示 [完成] 旗標，然後按一下 [儲存]，則**項目**會從未完成的工作清單中移除。

	![[索引] 檢視的螢幕擷取畫面，內含勾選的 [已完成] 方塊](./media/documentdb-dotnet-application/image27.png)

4. 完成測試應用程式後，按 Ctrl + F5 停止偵錯應用程式。您現在可以開始進行部署。

##<a name="_Toc395637774"></a>步驟 7：將應用程式部署至 Azure 網站

您已經擁有可在 DocumentDB 正常運作的完整應用程式，我們現在要將此 Web 應用程式部署至 Azure 網站。如果您在建立空白 ASP.NET MVC 專案時選取了 [在雲端託管]，則 Visual Studio 可讓這項作業變得十分簡單，並為您完成大部分的工作。

1. 若要發佈此應用程式，您只需要以滑鼠右鍵按一下 [方案總管] 上的專案，然後按一下 [發佈] 即可。

    ![[方案總管] 中 [發佈] 選項的螢幕擷取畫面](./media/documentdb-dotnet-application/image28.png)

2. 系統應該已根據您的認證設定好所有項目；實際上，系統已在 Azure 中建立位於所顯示的 [目的地 URL] 的網站，您只需要按一下 [發佈] 即可。

    ![Visual Studio [發佈 Web] 對話方塊的螢幕擷取畫面](./media/documentdb-dotnet-application/image29.png)

幾秒後，Visual Studio 便會發佈 Web 應用程式並啟動瀏覽器，您可以在瀏覽器中看到您方便好用的應用程式已在 Azure 中執行！

##<a name="_Toc395637775"></a>後續步驟

恭喜！ 您剛剛使用 Azure DocumentDB 建置您的第一個 ASP.NET MVC 應用程式，並將它發佈至 Azure 網站。您可以從 [GitHub][] 下載或複製完整應用程式 (包括不包含在本教學課程中的詳細資料和刪除功能) 的原始程式碼。所以如果您想要將程式碼加入您的應用程式，請抓取程式碼，並將它加入這個應用程式。

若要將其他功能加入至您的應用程式，請檢閱[文件 DB .NET 程式庫](http://msdn.microsoft.com/library/azure/dn783362.aspx)中提供的 API，並歡迎您貢獻到 [GitHub][] 上的 DocumentDB .NET 程式庫。


[*]: https://microsoft.sharepoint.com/teams/DocDB/Shared%20Documents/Documentation/Docs.LatestVersions/PicExportError
[Visual Studio Express]: http://www.visualstudio.com/products/visual-studio-express-vs.aspx
[Microsoft Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
[防止跨網站偽造要求]: http://go.microsoft.com/fwlink/?LinkID=517254
[ASP.NET MVC 中的基本 CRUD 作業]: http://go.microsoft.com/fwlink/?LinkId=317598
[GitHub]: https://github.com/Azure-Samples/documentdb-net-todo-app

<!---HONumber=Oct15_HO4-->