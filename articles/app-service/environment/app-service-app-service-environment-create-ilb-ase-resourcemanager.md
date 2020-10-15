---
title: Creare ILB ASE V1
description: Creare un ambiente del servizio app con un servizio di bilanciamento del carico interno (ILB ASE). Questo documento è rivolto solo ai clienti che usano l'ambiente del servizio app v1 legacy.
author: stefsch
ms.assetid: 091decb6-b0de-42a1-9f2f-c18d9b2e67df
ms.topic: article
ms.date: 07/11/2017
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 2a03b791f37868010e107214ddcb7cf42174e4e1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85833554"
---
# <a name="how-to-create-an-ilb-ase-using-azure-resource-manager-templates"></a>Come creare un ambiente del servizio app con servizio di bilanciamento del carico interno usando modelli di Azure Resource Manager

> [!NOTE] 
> Questo articolo riguarda l'ambiente del servizio app v1. È disponibile una versione più recente dell'ambiente del servizio app più facile da usare ed eseguita in un'infrastruttura più potente. Per ulteriori informazioni sulla nuova versione, iniziare con l' [Introduzione al ambiente del servizio app](intro.md).
>

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Panoramica
È possibile creare ambienti del servizio app con un indirizzo interno di rete virtuale anziché un indirizzo VIP pubblico.  L'indirizzo interno viene messo a disposizione da un componente di Azure denominato servizio di bilanciamento del carico interno (ILB).  È possibile creare un ambiente del servizio app con servizio di bilanciamento del carico interno usando il portale di Azure.  L'ambiente può anche essere creato con l'automazione, usando i modelli di Azure Resource Manager.  Questo articolo illustra i passaggi e la sintassi necessari per creare un ambiente del servizio app con servizio di bilanciamento del carico interno con i modelli di Azure Resource Manager.

L'automazione della creazione di un ambiente del servizio app con servizio di bilanciamento del carico interno prevede tre passaggi:

1. Viene prima creato l'ambiente del servizio app di base in una rete virtuale usando un indirizzo di servizio di bilanciamento del carico interno anziché un indirizzo VIP pubblico.  Nell'ambito di questo passaggio viene assegnato un nome di dominio radice all'ambiente del servizio app con servizio di bilanciamento del carico interno.
2. Dopo aver creato l'ambiente del servizio app ILB, viene caricato un certificato TLS/SSL.  
3. Il certificato TLS/SSL caricato viene assegnato in modo esplicito all'ambiente del servizio app ILB come certificato TLS/SSL "predefinito".  Questo certificato TLS/SSL verrà usato per il traffico TLS verso le app nell'ambiente del servizio app ILB quando le app vengono risolte usando il dominio radice comune assegnato all'ambiente del servizio app, ad esempio `https://someapp.mycustomrootcomain.com`

## <a name="creating-the-base-ilb-ase"></a>Creazione dell'ambiente del servizio app con servizio di bilanciamento del carico interno
Un modello di Azure Resource Manager di esempio e il file dei parametri associati sono disponibili in GitHub [qui][quickstartilbasecreate].

La maggior parte dei parametri del file *azuredeploy.parameters.json* è comune alla creazione sia dell'ambiente del servizio app con servizio di bilanciamento del carico interno che dell'ambiente del servizio app associato a un indirizzo VIP pubblico.  L'elenco seguente indica parametri importanti o specifici per la creazione di un ambiente del servizio app con servizio di bilanciamento del carico interno:

* *internalLoadBalancingMode*: nella maggior parte dei casi impostare questa impostazione su 3, il che significa che il traffico HTTP/HTTPS sulle porte 80/443 e le porte del canale di controllo/dati ascoltate dal servizio FTP nell'ambiente del servizio app saranno associati a un indirizzo interno della rete virtuale allocato ILB.  Se questa proprietà viene invece impostata su 2, solo le porte correlate al servizio FTP, ovvero i canali di controllo e i canali dei dati, saranno associate a un indirizzo del servizio di bilanciamento del carico interno, mentre il traffico HTTP/HTTPS rimarrà associato all'indirizzo VIP pubblico.
* *dnsSuffix*: questo parametro indica il dominio radice predefinito che verrà assegnato all'ambiente del servizio app.  Nella variante pubblica del servizio app di Azure, il dominio radice predefinito per tutte le app Web è *azurewebsites.net*.  Poiché tuttavia un ambiente del servizio app con servizio di bilanciamento del carico interno è interno alla rete virtuale di un cliente, non ha senso usare il dominio radice predefinito del servizio pubblico.  Un ambiente del servizio app con servizio di bilanciamento del carico interno deve invece avere un dominio radice predefinito appropriato per la rete virtuale interna dell'azienda.  Un'ipotetica azienda Contoso Corporation potrebbe ad esempio usare un dominio radice predefinito *internal-contoso.com* per le app che devono essere risolvibili e accessibili solo nella rete virtuale di Contoso. 
* *ipSslAddressCount*: questo parametro viene automaticamente impostato sul valore predefinito di 0 nel file *azuredeploy.json* perché gli ambienti del servizio app con servizio di bilanciamento del carico interno hanno un solo indirizzo del servizio di bilanciamento del carico interno.  Non sono disponibili indirizzi IP SSL espliciti per un ambiente del servizio app con servizio di bilanciamento del carico interno, quindi il pool di indirizzi IP SSL per un ambiente del servizio app con servizio di bilanciamento del carico interno deve essere impostato su zero, in caso contrario si verificherà un errore di provisioning. 

Una volta compilato il *azuredeploy.parameters.js* nel file per un ambiente del servizio app ILB, è possibile creare l'ambiente del servizio app ILB usando il frammento di codice di PowerShell seguente.  Modificare i percorsi dei file in modo che corrispondano a quelli in cui si trovano i file del modello di Azure Resource Manager nel computer.  Indicare anche i valori per il nome della distribuzione di Azure Resource Manager e il nome del gruppo di risorse.

```azurepowershell-interactive
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

Dopo aver inviato il modello di Azure Resource Manager, la creazione dell'ambiente del servizio app con servizio di bilanciamento del carico interno richiederà alcune ore.  Al termine della creazione, l'ambiente del servizio app con servizio di bilanciamento del carico interno verrà visualizzato nel portale nell'elenco di ambienti del servizio app per la sottoscrizione che ha attivato la distribuzione.

## <a name="uploading-and-configuring-the-default-tlsssl-certificate"></a>Caricamento e configurazione del certificato TLS/SSL "predefinito"
Dopo aver creato l'ambiente del servizio app ILB, è necessario associare un certificato TLS/SSL all'ambiente del servizio app come certificato TLS/SSL "predefinito" usato per stabilire le connessioni TLS/SSL alle app.  Continuando con l'esempio ipotetico di Contoso Corporation, se il suffisso DNS predefinito dell'ambiente del servizio app è *Internal-contoso.com*, una connessione a *`https://some-random-app.internal-contoso.com`* richiede un certificato TLS/SSL valido per **. Internal-contoso.com*. 

Esistono diversi modi per ottenere un certificato TLS/SSL valido, incluse le autorità di certificazione interne, l'acquisto di un certificato da un emittente esterno e l'uso di un certificato autofirmato.  Indipendentemente dall'origine del certificato TLS/SSL, è necessario configurare correttamente gli attributi del certificato seguenti:

* *Subject*: questo attributo deve essere impostato su **.your-root-domain-here.com*.
* *Nome alternativo soggetto*: questo attributo deve includere sia **.your-root-domain-here.com* che **.scm.your-root-domain-here.com*.  Il motivo della seconda voce è che le connessioni TLS al sito SCM/Kudu associato a ogni app verranno effettuate usando un indirizzo nel formato *Your-App-Name.SCM.your-root-domain-here.com*.

Con un certificato TLS/SSL valido a disposizione, sono necessari due passaggi preliminari aggiuntivi.  Il certificato TLS/SSL deve essere convertito/salvato come file con estensione pfx.  Il file con estensione pfx deve includere tutti i certificati intermedi e radice ed essere protetto con una password.

Il file PFX risultante deve quindi essere convertito in una stringa Base64 perché il certificato TLS/SSL verrà caricato usando un modello di Azure Resource Manager.  Poiché i modelli di Azure Resource Manager sono file di testo, il file con estensione pfx deve essere convertito in una stringa con codifica Base64 per essere incluso come parametro del modello.

Il frammento di codice di PowerShell riportato di seguito illustra un esempio di generazione di un certificato autofirmato, l'esportazione del certificato come file con estensione pfx, la conversione del file pfx in una stringa con codifica Base64 e il salvataggio della stringa con codifica Base64 in un file separato.  Il codice PowerShell per la codifica Base64 è stato adattato dal [Blog di script di PowerShell][examplebase64encoding].

```azurepowershell-interactive
$certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

$certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
$password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

$fileName = "exportedcert.pfx"
Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     

$fileContentBytes = get-content -encoding byte $fileName
$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
$fileContentEncoded | set-content ($fileName + ".b64")
```

Una volta che il certificato TLS/SSL è stato generato e convertito in una stringa con codifica Base64, è possibile usare il modello di esempio Azure Resource Manager in GitHub per la [configurazione del certificato TLS/SSL predefinito][configuringDefaultSSLCertificate] .

I parametri del file *azuredeploy.parameters.json* sono elencati di seguito:

* *appServiceEnvironmentName*: nome dell'ambiente del servizio app ILB da configurare.
* *existingAseLocation*: stringa di testo contenente l'area di Azure in cui è stato distribuito l'ambiente del servizio app ILB.  Ad esempio "Stati Uniti centro-meridionali".
* *pfxBlobString*: rappresentazione di stringa con codifica Base64 del file con estensione pfx.  Usando il frammento di codice indicato in precedenza, la stringa contenuta in "exportedcert.pfx.b64" dovrà essere copiata e incollata come valore dell'attributo *pfxBlobString* .
* *password*: password usata per proteggere il file con estensione pfx.
* *CertificateThumbprint*: identificazione personale del certificato.  Se si recupera questo valore da PowerShell, ad esempio *$Certificate. Identificazione personale* del frammento di codice precedente), è possibile utilizzare il valore così com'è.  Se tuttavia si copia il valore dalla finestra di dialogo del certificato di Windows, rimuovere gli spazi estranei.  Il valore di *CertificateThumbprint* dovrebbe essere simile al seguente: AF3143EB61D43F6727842115BB7F17BBCECAECAE
* *Certificate*: identificatore di stringa descrittivo scelto per l'identità del certificato.  Il nome viene usato come parte dell'identificatore di Azure Resource Manager univoco per l'entità *Microsoft. Web/Certificates* che rappresenta il certificato TLS/SSL.  Il nome **deve** terminare con il suffisso seguente: \_yourASENameHere_InternalLoadBalancingASE.  Questo suffisso viene usato dal portale per indicare che il certificato è usato per la protezione di un ambiente del servizio app abilitato al bilanciamento del carico interno.

Un esempio abbreviato di *azuredeploy.parameters.json* è illustrato di seguito:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json",
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
```

Una volta compilato ilazuredeploy.parameters.jsnel file, il certificato TLS/SSL predefinito può essere configurato usando il frammento di codice * di* PowerShell seguente.  Modificare i percorsi dei file in modo che corrispondano a quelli in cui si trovano i file del modello di Azure Resource Manager nel computer.  Indicare anche i valori per il nome della distribuzione di Azure Resource Manager e il nome del gruppo di risorse.

```azurepowershell-interactive
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

Dopo l'invio del modello di Azure Resource Manager, ci vorranno circa 40 minuti per ogni front-end dell'ambiente del servizio app per apportare la modifica.  Per un ambiente del servizio app di dimensioni predefinite che usa due front-end, ad esempio, l'operazione richiederà all'incirca un'ora e venti minuti.  Durante l'esecuzione del modello, l'ambiente del servizio app non potrà essere ridimensionato.  

Una volta completato il modello, è possibile accedere alle app nell'ambiente del servizio app ILB tramite HTTPS e le connessioni verranno protette usando il certificato TLS/SSL predefinito.  Il certificato TLS/SSL predefinito verrà usato quando le app nell'ambiente del servizio app ILB vengono risolte usando una combinazione del nome dell'applicazione più il nome host predefinito.  Ad esempio *`https://mycustomapp.internal-contoso.com`* , utilizzerebbe il certificato TLS/SSL predefinito per **. Internal-contoso.com*.

Tuttavia, proprio come le applicazioni in esecuzione nel servizio pubblico multi-tenant, gli sviluppatori possono anche configurare nomi host personalizzati per le singole app e quindi configurare associazioni di certificati TLS/SSL SNI univoche per le singole app.  

## <a name="getting-started"></a>Introduzione
Per iniziare a usare gli ambienti del servizio app, vedere [Introduzione all'ambiente del servizio app](app-service-app-service-environment-intro.md)

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[quickstartilbasecreate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-create/
[examplebase64encoding]: https://powershellscripts.blogspot.com/2007/02/base64-encode-file.html 
[configuringDefaultSSLCertificate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl/ 

