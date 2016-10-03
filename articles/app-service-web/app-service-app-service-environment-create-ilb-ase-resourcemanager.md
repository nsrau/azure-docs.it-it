<properties 
	pageTitle="Come creare un ambiente del servizio app con servizio di bilanciamento del carico interno usando modelli di Azure Resource Manager | Microsoft Azure" 
	description="Informazioni su come creare un ambiente del servizio app con servizio di bilanciamento del carico interno usando modelli di Azure Resource Manager." 
	services="app-service" 
	documentationCenter="" 
	authors="stefsch" 
	manager="nirma" 
	editor=""/> 

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/21/2016" 
	ms.author="stefsch"/> 

# Come creare un ambiente del servizio app con servizio di bilanciamento del carico interno usando modelli di Azure Resource Manager

## Overview ##
È possibile creare ambienti del servizio app con un indirizzo interno di rete virtuale anziché un indirizzo VIP pubblico. L'indirizzo interno viene messo a disposizione da un componente di Azure denominato servizio di bilanciamento del carico interno (ILB). È possibile creare un ambiente del servizio app con servizio di bilanciamento del carico interno usando il portale di Azure. L'ambiente può anche essere creato con l'automazione, usando i modelli di Azure Resource Manager. Questo articolo illustra i passaggi e la sintassi necessari per creare un ambiente del servizio app con servizio di bilanciamento del carico interno con i modelli di Azure Resource Manager.

L'automazione della creazione di un ambiente del servizio app con servizio di bilanciamento del carico interno prevede tre passaggi:
1. Viene prima creato l'ambiente del servizio app di base in una rete virtuale usando un indirizzo di servizio di bilanciamento del carico interno anziché un indirizzo VIP pubblico. Nell'ambito di questo passaggio viene assegnato un nome di dominio radice all'ambiente del servizio app con servizio di bilanciamento del carico interno.
2. Dopo aver creato l'ambiente del servizio app con servizio di bilanciamento del carico interno, viene caricato un certificato SSL.
3. Il certificato SSL caricato viene assegnato in modo esplicito all'ambiente del servizio app con servizio di bilanciamento del carico interno come certificato "predefinito". Questo certificato SSL verrà usato per il traffico SSL verso le app nell'ambiente del servizio app con servizio di bilanciamento del carico interno quando si usa il dominio radice comune assegnato all'ambiente del servizio app, ad esempio https://someapp.mycustomrootcomain.com

## Creazione dell'ambiente del servizio app con servizio di bilanciamento del carico interno ##
Un modello di Azure Resource Manager di esempio e il file dei parametri associati sono disponibili in GitHub [qui][quickstartilbasecreate].

La maggior parte dei parametri del file *azuredeploy.parameters.json* è comune alla creazione sia dell'ambiente del servizio app con servizio di bilanciamento del carico interno che dell'ambiente del servizio app associato a un indirizzo VIP pubblico. L'elenco seguente indica parametri importanti o specifici per la creazione di un ambiente del servizio app con servizio di bilanciamento del carico interno:


- *interalLoadBalancingMode*: impostare nella maggior parte dei casi su 3, a indicare che sia il traffico HTTP/HTTPS sulle porte 80/443 che le porte dei canali di controllo/dati ascoltati dal servizio FTP nell'ambiente del servizio app saranno associati a un indirizzo interno della rete virtuale allocato dal servizio di bilanciamento del carico interno. Se questa proprietà viene invece impostata su 2, solo le porte correlate al servizio FTP, ovvero i canali di controllo e i canali dei dati, saranno associate a un indirizzo del servizio di bilanciamento del carico interno, mentre il traffico HTTP/HTTPS rimarrà associato all'indirizzo VIP pubblico.
-  *dnsSuffix*: questo parametro indica il dominio radice predefinito che verrà assegnato all'ambiente del servizio app. Nella variante pubblica del servizio app di Azure, il dominio radice predefinito per tutte le app Web è *azurewebsites.net*. Poiché tuttavia un ambiente del servizio app con servizio di bilanciamento del carico interno è interno alla rete virtuale di un cliente, non ha senso usare il dominio radice predefinito del servizio pubblico. Un ambiente del servizio app con servizio di bilanciamento del carico interno deve invece avere un dominio radice predefinito appropriato per la rete virtuale interna dell'azienda. Un'ipotetica azienda Contoso Corporation potrebbe ad esempio usare un dominio radice predefinito *internal-contoso.com* per le app che devono essere risolvibili e accessibili solo nella rete virtuale di Contoso.
-  *ipSslAddressCount*: questo parametro viene automaticamente impostato sul valore predefinito di 0 nel file *azuredeploy.json* perché gli ambienti del servizio app con servizio di bilanciamento del carico interno hanno un solo indirizzo del servizio di bilanciamento del carico interno. Non sono disponibili indirizzi IP SSL espliciti per un ambiente del servizio app con servizio di bilanciamento del carico interno, quindi il pool di indirizzi IP SSL per un ambiente del servizio app con servizio di bilanciamento del carico interno deve essere impostato su zero, in caso contrario si verificherà un errore di provisioning.

Dopo che il file *azuredeploy.parameters.json* è stato compilato per un ambiente del servizio app con servizio di bilanciamento del carico interno, l'ambiente potrà essere quindi creato con il seguente frammento di codice Powershell. Modificare i parametri PATH dei file per fare in modo che corrispondano al percorso dei file dei modelli di Azure Resource Manager nel computer. Indicare anche i valori per il nome della distribuzione di Azure Resource Manager e il nome del gruppo di risorse.

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"
    
    New-AzureRmResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

Dopo aver inviato il modello di Azure Resource Manager, la creazione dell'ambiente del servizio app con servizio di bilanciamento del carico interno richiederà alcune ore. Al termine della creazione, l'ambiente del servizio app con servizio di bilanciamento del carico interno verrà visualizzato nel portale nell'elenco di ambienti del servizio app per la sottoscrizione che ha attivato la distribuzione.

## Caricamento e configurazione del certificato SSL "predefinito" ##

Dopo aver creato l'ambiente del servizio app con servizio di bilanciamento del carico interno è necessario associare un certificato SSL all'ambiente del servizio app come certificato SSL "predefinito", per stabilire le connessioni SSL alle app. Proseguendo con l'esempio dell'ipotetica Contoso Corporation, se il suffisso DNS predefinito dell'ambiente del servizio app è *internal-contoso.com*, una connessione a *https://some-random-app.internal-contoso.com* richiede un certificato SSL valido per **.internal-contoso.com*.

Esistono diversi modi per ottenere un certificato SSL valido, tra cui CA interne, l'acquisto di un certificato da un'autorità di certificazione esterna e l'uso di un certificato autofirmato. Indipendentemente dall'origine del certificato SSL è necessario configurare correttamente gli attributi del certificato seguenti:

- *Subject*: questo attributo deve essere impostato su **.your-root-domain-here.com*
- *Subject Alternative Name*: questo attributo deve includere sia **.your-root-domain-here.com* che **.scm.your-root-domain-here.com*. La seconda voce è necessaria perché le connessioni SSL al sito SCM/Kudu associato a ogni app verranno eseguite con un indirizzo nel formato *your-app-name.scm.your-root-domain-here.com*.

Dopo aver ottenuto un certificato SSL valido sono necessari altri due passaggi preliminari. Il certificato SSL deve essere convertito/salvato come file con estensione pfx. Il file con estensione pfx deve includere tutti i certificati intermedi e radice ed essere protetto con una password.

Il file con estensione pfx dovrà quindi essere convertito in una stringa con codifica Base64 perché il certificato SSL verrà caricato usando un modello di Azure Resource Manager. Poiché i modelli di Azure Resource Manager sono file di testo, il file con estensione pfx deve essere convertito in una stringa con codifica Base64 per essere incluso come parametro del modello.

Il frammento di codice Powershell riportato di seguito illustra un esempio di generazione di un certificato autofirmato, esportazione del certificato come file con estensione pfx, conversione del file con estensione pfx in una stringa con codifica Base64 e quindi salvataggio della stringa con codifica Base64 in un file separato. Il codice Powershell per la codifica Base64 è stato adattato dal [blog di script Powershell][examplebase64encoding].

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

    $certThumbprint = "cert:\localMachine\my" + $certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

    $fileName = "exportedcert.pfx"
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     
    
    $fileContentBytes = get-content -encoding byte $fileName
    $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
    $fileContentEncoded | set-content ($fileName + ".b64")
    
Dopo aver generato il certificato SSL e averlo convertito in una stringa con codifica Base64, è possibile usare il modello di esempio di Azure Resource Manager disponibile in GitHub per la [configurazione del certificato SSL predefinito][configuringDefaultSSLCertificate].

I parametri del file *azuredeploy.parameters.json* sono elencati di seguito:

- *appServiceEnvironmentName*: nome dell'ambiente del servizio app con servizio di bilanciamento del carico interno in fase di configurazione.
- *existingAseLocation*: stringa di testo contenente l'area di Azure in cui è stato distribuito l'ambiente del servizio app con servizio di bilanciamento del carico interno. Ad esempio "Stati Uniti centro-meridionali".
- *pfxBlobString*: rappresentazione del file con estensione pfx sotto forma di stringa con codifica Base64. Usando il frammento di codice indicato in precedenza, la stringa contenuta in "exportedcert.pfx.b64" dovrà essere copiata e incollata come valore dell'attributo *pfxBlobString*.
- *password*: password usata per la protezione del file con estensione pfx.
- *certificateThumbprint*: identificazione personale del certificato. Se si recupera questo valore da Powershell, ad esempio *$certificate.Thumbprint* nel frammento di codice precedente, è possibile usare il valore così com'è. Se tuttavia si copia il valore dalla finestra di dialogo del certificato di Windows, rimuovere gli spazi estranei. Il valore di *certificateThumbprint* sarà simile a AF3143EB61D43F6727842115BB7F17BBCECAECAE
- *certificateName*: identificatore di stringa descrittivo scelto dall'utente per identificare il certificato. Il nome viene usato nell'ambito dell'identificatore univoco di Azure Resource Manager per l'entità *Microsoft.Web/certificates* che rappresenta il certificato SSL. Il nome **deve** terminare con il suffisso seguente: \_yourASENameHere\_InternalLoadBalancingASE. Questo suffisso viene usato dal portale per indicare che il certificato è usato per la protezione di un ambiente del servizio app abilitato al bilanciamento del carico interno.


Un esempio abbreviato di *azuredeploy.parameters.json* è illustrato di seguito:


    {
         "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json",
         "contentVersion": "1.0.0.0",
         "parameters": {
              "appServiceEnvironmentName": {
                   "value": "yourASENameHere"
              },
              "existingAseLocation": {
                   "value": "East US 2"
              },
              "pfxBlobString": {
                   "value": "MIIKcAIBAz...snip...snip...pkCAgfQ"
              },
              "password": {
                   "value": "PASSWORDGOESHERE"
              },
              "certificateThumbprint": {
                   "value": "AF3143EB61D43F6727842115BB7F17BBCECAECAE"
              },
              "certificateName": {
                   "value": "DefaultCertificateFor_yourASENameHere_InternalLoadBalancingASE"
              }
         }
    }

Dopo che il file *azuredeploy.parameters.json* è stato compilato, il certificato SSL predefinito può essere configurato con il frammento di codice Powershell seguente. Modificare i parametri PATH dei file per fare in modo che corrispondano al percorso dei file dei modelli di Azure Resource Manager nel computer. Indicare anche i valori per il nome della distribuzione di Azure Resource Manager e il nome del gruppo di risorse.

    $templatePath="PATH\azuredeploy.json"
    $parameterPath="PATH\azuredeploy.parameters.json"
    
    New-AzureRmResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath

Dopo l'invio del modello di Azure Resource Manager, l'applicazione della modifica richiederà all'incirca 40 minuti per ogni front-end dell'ambiente del servizio app. Per un ambiente del servizio app di dimensioni predefinite che usa due front-end, ad esempio, l'operazione richiederà all'incirca un'ora e venti minuti. Durante l'esecuzione del modello, l'ambiente del servizio app non potrà essere ridimensionato.

Al termine dell'esecuzione del modello sarà possibile accedere alle app nell'ambiente del servizio app con servizio di bilanciamento del carico interno tramite HTTPS e le connessioni saranno protette con il certificato SSL predefinito. Il certificato SSL predefinito verrà usato quando le app nell'ambiente del servizio app con servizio di bilanciamento del carico interno vengono indirizzate usando una combinazione di nome applicazione e nome host predefinito. *https://mycustomapp.internal-contoso.com*, ad esempio, userà il certificato SSL predefinito per **.internal-contoso.com*.

Proprio come le app in esecuzione nel servizio multi-tenant pubblico, gli sviluppatori possono tuttavia anche configurare nomi host personalizzati e associazioni univoche a certificati SNI SSL per le singole app.


## Introduzione

Per iniziare a usare gli ambienti del servizio app, vedere [Introduzione all'ambiente del servizio app](app-service-app-service-environment-intro.md).

Tutti gli articoli e le procedure sugli ambienti del servizio app sono disponibili nel [file LEGGIMI per gli ambienti di servizio dell'applicazione](../app-service/app-service-app-service-environments-readme.md).

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- LINKS --> 
[quickstartilbasecreate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-create/
[examplebase64encoding]: http://powershellscripts.blogspot.com/2007/02/base64-encode-file.html
[configuringDefaultSSLCertificate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl/
 

<!---HONumber=AcomDC_0921_2016-->