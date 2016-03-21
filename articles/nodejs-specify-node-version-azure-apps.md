<properties
	pageTitle="Specifica di una versione di Node.js"
	description="Informazioni su come specificare la versione di Node. js usata da Siti Web e Servizi cloud di Azure"
	services=""
	documentationCenter="nodejs"
	authors="rmcmurray"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="multiple"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="nodejs"
	ms.topic="article"
	ms.date="03/04/2016"
	ms.author="robmcm"/>

# Specifica di una versione di Node.js in un'applicazione Azure

Quando si ospita un'applicazione Node.js, può essere necessario assicurarsi che utilizzi una versione specifica di Node.js. Questa operazione può essere eseguita in vari modi per le applicazioni ospitate in Azure.

##Versioni predefinite

Le versioni di Node.js fornite da Azure vengono aggiornate costantemente. Se non diversamente specificato, verrà usata la versione più recente.

> [AZURE.NOTE] Se l'applicazione è ospitata in un servizio cloud di Azure (ruolo di lavoro o Web) ed è la prima volta che si distribuisce l'applicazione, Azure tenterà di usare la stessa versione di Node.js installata nell'ambiente di sviluppo, se questa corrisponde a une delle versioni predefinite disponibili.

##Controllo delle versioni con package.json

È possibile specificare la versione di Node.js da usare aggiungendo il codice seguente al file **package.json**:

	"engines":{"node":version}

Dove *version* è lo specifico numero di versione da usare. È possibile specificare condizioni più complesse per la versione, ad esempio:

	"engines":{"node": "0.6.22 || 0.8.x"}

Poiché la 0.6.22 non è una delle versioni disponibili nell'ambiente host, verrà utilizzata la versione più recente della serie 0.8 disponibile, ovvero la 0.8.4.

##Controllo delle versioni di Siti Web con Impostazioni app
Se si ospita l'applicazione in un sito Web, è possibile impostare la variabile di ambiente **WEBSITE\_NODE\_DEFAULT\_VERSION** sulla versione desiderata.

##Controllo delle versioni dei servizi cloud con PowerShell

Se l'applicazione è ospitata in un servizio cloud e si sta distribuendo l'applicazione utilizzando Azure PowerShell, è possibile sostituire la versione predefinita di Node.js utilizzando il cmdlet di PowerShell **Set-AzureServiceProjectRole**. Ad esempio:

	Set-AzureServiceProjectRole WebRole1 Node 0.8.4

Si noti che i parametri nell'istruzione precedente fanno la distinzione tra maiuscole e minuscole. È possibile verificare di aver selezionato la versione corretta di Node.js controllando la proprietà **engines** nel **package.json** del ruolo.

È inoltre possibile usare **Get-AzureServiceProjectRoleRuntime** per recuperare un elenco delle versioni di Node.js disponibili per le applicazioni ospitate come servizi cloud. Verificare sempre che la versione di Node. js dipenda da se il progetto è incluso nell'elenco.

##Uso di una versione personalizzata con i siti Web di Azure

Anche se in Azure sono disponibili svariate versioni predefinite di Node.js, potrebbe essere necessario usare una versione non disponibile per impostazione predefinita. Se l'applicazione è ospitata come sito Web di Azure, è possibile eseguire l'operazione usando il file **iisnode.yml**. I passaggi successivi illustrano la procedura per l'uso di una versione personalizzata di Node.Js con un sito Web di Azure:

1. Creare una nuova directory e quindi creare un file **server.js** al suo interno. Il contenuto del file deve essere il seguente **server.js**:

		var http = require('http');
		http.createServer(function(req,res) {
		  res.writeHead(200, {'Content-Type': 'text/html'});
		  res.end('Hello from Azure running node version: ' + process.version + '</br>');
		}).listen(process.env.PORT || 3000);

	Questo consentirà di visualizzare la versione di Node.js utilizzata durante l'esplorazione del sito Web.

2. Creare un nuovo sito Web e prendere nota del nome del sito. Nel comando seguente, ad esempio, gli [strumenti da riga di comando di Azure] vengono utilizzati per creare un nuovo sito Web di Azure denominato **mywebsite** e quindi per abilitare un repository Git per il sito Web.

		azure site create mywebsite --git

3. Creare una nuova directory denominata **bin** come figlio della directory che contiene il file **server.js**.

4. Scaricare la specifica versione di **node.exe** (per Windows) che si desidera utilizzare con l'applicazione. Nell'esempio seguente viene usato **curl** per scaricare la versione 0.8.1:

		curl -O http://nodejs.org/dist/v0.8.1/node.exe

	Salvare il file **node.exe** nella cartella **bin** creata in precedenza.

5. Creare un file **iisnode.yml** nella stessa directory del file **server.js** e quindi aggiungere il contenuto seguente al file **iisnode.yml**:

		nodeProcessCommandLine: "D:\home\site\wwwroot\bin\node.exe"

	Questo percorso corrisponde alla posizione in cui sarà situato il file **node.exe** all'interno del progetto dopo la pubblicazione dell'applicazione nel sito Web di Azure.

6. Pubblicare l'applicazione. Ad esempio, poiché in precedenza è stato creato un nuovo sito Web con il parametro --git, i comandi seguenti consentiranno di aggiungere i file dell'applicazione al repository Git locale e quindi di effettuarne il push nel repository del sito Web:

		git add .
		git commit -m "testing node v0.8.1"
		git push azure master

	Dopo la pubblicazione dell'applicazione, aprire il sito Web in un browser. Dovrebbe essere visualizzato il messaggio "Hello from Azure running node version: v0.8.1".

##Passaggi successivi

Dopo avere appreso come specificare la versione di Node.js utilizzata dall'applicazione, per ulteriori informazioni vedere gli articoli che illustrano come [utilizzare i moduli], [creare e distribuire un sito Web Node.js] e [Come utilizzare gli strumenti da riga di comando di Azure per Mac e Linux].

Per ulteriori informazioni, vedere il [Centro per sviluppatori di Node.js](/develop/nodejs/).

[Come utilizzare gli strumenti da riga di comando di Azure per Mac e Linux]: xplat-cli-install.md
[strumenti da riga di comando di Azure]: xplat-cli-install.md
[utilizzare i moduli]: nodejs-use-node-modules-azure-apps.md
[creare e distribuire un sito Web Node.js]: web-sites-nodejs-develop-deploy-mac.md

<!---HONumber=AcomDC_0309_2016-->