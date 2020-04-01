---
title: Eseguire la migrazione dalla versione 1 alla versione 2 - Gateway applicazione di AzureMigrate from v1 to v2 - Azure Application Gateway
description: Questo articolo illustra come eseguire la migrazione di Gateway applicazione di Azure e Web Application Firewall da v1 a v2
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 03/31/2020
ms.author: victorh
ms.openlocfilehash: 96f3825288846e86771ef3907eb4da4e58630df3
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80475182"
---
# <a name="migrate-azure-application-gateway-and-web-application-firewall-from-v1-to-v2"></a>Eseguire la migrazione del gateway applicazione di Azure e del firewall applicazione Web dalla versione 1 alla v2Migrate Azure Application Gateway and Web Application Firewall from v1 to v2

[Il gateway applicazione di Azure e Il Web Application Firewall (WAF) v2](application-gateway-autoscaling-zone-redundant.md) sono ora disponibili, offrendo funzionalità aggiuntive, ad esempio la scalabilità automatica e la ridondanza delle zone di disponibilità. Tuttavia, i gateway versione 1 esistenti non vengono aggiornati automaticamente alla versione 2. Se si desidera eseguire la migrazione dalla versione v1 alla v2, attenersi alla procedura descritta in questo articolo.

Esistono due fasi di una migrazione:There are two stages in a migration:

1. Eseguire la migrazione della configurazione
2. Eseguire la migrazione del traffico clientMigrate the client traffic

In questo articolo viene illustrata la migrazione della configurazione. La migrazione del traffico client varia a seconda dell'ambiente specifico. Tuttavia, [vengono fornite](#migrate-client-traffic)alcune raccomandazioni generali di alto livello .

## <a name="migration-overview"></a>Panoramica della migrazione

È disponibile uno script di Azure PowerShell che esegue le operazioni seguenti:An Azure PowerShell script is available that does the following:

* Crea un nuovo gateway di Standard_v2 o WAF_v2 in una subnet di rete virtuale specificata.
* Copia senza problemi la configurazione associata al gateway V1 Standard o WAF nel Standard_V2 o nel gateway WAF_V2 appena creato.

### <a name="caveatslimitations"></a>Avvertenze - Limitazioni

* Il nuovo gateway v2 ha nuovi indirizzi IP pubblici e privati. Non è possibile spostare senza problemi gli indirizzi IP associati al gateway v1 esistente nella versione 2. Tuttavia, è possibile allocare un indirizzo IP pubblico o privato esistente (non allocato) al nuovo gateway v2.
* È necessario fornire uno spazio di indirizzi IP per un'altra subnet all'interno della rete virtuale in cui si trova il gateway v1. Lo script non può creare il gateway v2 in subnet esistenti che dispongono già di un gateway v1. Tuttavia, se la subnet esistente dispone già di un gateway v2, potrebbe comunque funzionare a condizione che sia disponibile spazio di indirizzi IP sufficiente.
* Per eseguire la migrazione di una configurazione SSL, è necessario specificare tutti i certificati SSL utilizzati nel gateway v1.
* Se la modalità FIPS è abilitata per il gateway V1, non verrà eseguita la migrazione al nuovo gateway v2. La modalità FIPS non è supportata nella versione 2.
* V2 non supporta IPv6, pertanto i gateway v1 abilitati per IPv6 non vengono migrati. Se si esegue lo script, potrebbe non essere completato.
* Se il gateway v1 ha solo un indirizzo IP privato, lo script crea un indirizzo IP pubblico e un indirizzo IP privato per il nuovo gateway v2. I gateway v2 attualmente non supportano solo indirizzi IP privati.
* Le intestazioni con nomi contenenti nomi diversi da lettere, cifre, trattini e caratteri di sottolineatura non vengono passate all'applicazione. Questo vale solo per i nomi delle intestazioni, non per i valori di intestazione. Questo è un cambiamento sostanziale rispetto alla v1.

## <a name="download-the-script"></a>Scarica lo script

Scaricare lo script di migrazione da [PowerShell Gallery](https://www.powershellgallery.com/packages/AzureAppGWMigration).

## <a name="use-the-script"></a>Utilizzare lo script

Sono disponibili due opzioni a seconda dell'installazione e delle preferenze dell'ambiente PowerShell locale:There are two options for you depending on your local PowerShell environment setup and preferences:

* Se i moduli di Azure Az non sono installati o non è consigliabile disinstallare i `Install-Script` moduli di Azure Az, l'opzione migliore consiste nell'usare l'opzione per eseguire lo script.
* Se è necessario mantenere i moduli di Azure Az, la soluzione migliore consiste nel scaricare lo script ed eseguirlo direttamente.

Per determinare se sono installati i `Get-InstalledModule -Name az`moduli di Azure Az, eseguire . Se non vedi alcun modulo Az installato, puoi `Install-Script` utilizzare il metodo .

### <a name="install-using-the-install-script-method"></a>Eseguire l'installazione utilizzando il metodo Install-Script

Per usare questa opzione, non è necessario che nel computer siano installati i moduli di Azure Az.To use this option, you must not have the Azure Az modules installed on your computer. Se sono installati, il comando seguente visualizza un errore. È possibile disinstallare i moduli di Azure Az oppure usare l'altra opzione per scaricare lo script manualmente ed eseguirlo.
  
Eseguire lo script con il comando seguente:

`Install-Script -Name AzureAppGWMigration`

Questo comando installa anche i moduli Az necessari.  

### <a name="install-using-the-script-directly"></a>Eseguire l'installazione utilizzando lo script direttamente

Se sono installati alcuni moduli di Azure Az e non è possibile disinstallarli o non si desidera disinstallarli, è possibile scaricare manualmente lo script usando la scheda **Download manuale** nel collegamento per il download dello script. Lo script viene scaricato come file nupkg non elaborato. Per installare lo script da questo file nupkg, vedere [Download manuale del pacchetto](/powershell/scripting/gallery/how-to/working-with-packages/manual-download).

Per eseguire lo script:

1. Usare `Connect-AzAccount` per connettersi ad Azure.Use to connect to Azure.

1. Utilizzare `Import-Module Az` per importare i moduli Az.

1. Eseguire `Get-Help AzureAppGWMigration.ps1` questa operazione per esaminare i parametri richiesti:

   ```
   AzureAppGwMigration.ps1
    -resourceId <v1 application gateway Resource ID>
    -subnetAddressRange <subnet space you want to use>
    -appgwName <string to use to append>
    -sslCertificates <comma-separated SSLCert objects as above>
    -trustedRootCertificates <comma-separated Trusted Root Cert objects as above>
    -privateIpAddress <private IP string>
    -publicIpResourceId <public IP name string>
    -validateMigration -enableAutoScale
   ```

   Parametri per lo script:
   * **resourceId: [String]: Obbligatorio** - Obbligatorio per l'ID risorsa di Azure per il gateway Standard v1 o WAF v1 esistente. Per trovare questo valore stringa, passare al portale di Azure, selezionare il gateway applicazione o la risorsa WAF e fare clic sul collegamento **Proprietà** per il gateway. L'ID risorsa si trova in quella pagina.

     È anche possibile eseguire i comandi di Azure PowerShell seguenti per ottenere l'ID risorsa:You can also run the following Azure PowerShell commands to get the Resource ID:

     ```azurepowershell
     $appgw = Get-AzApplicationGateway -Name <v1 gateway name> -ResourceGroupName <resource group Name> 
     $appgw.Id
     ```

   * **subnetAddressRange: [String]: Obbligatorio** - Si tratta dello spazio di indirizzi IP allocato (o che si desidera allocare) per una nuova subnet contenente il nuovo gateway v2. Questo deve essere specificato nella notazione CIDR. Ad esempio: 10.0.0.0/24. Non è necessario creare questa subnet in anticipo. Lo script lo crea automaticamente se non esiste.
   * **appgwName: [Stringa]: Facoltativo**. Si tratta di una stringa specificata da utilizzare come nome per il nuovo gateway Standard_v2 o WAF_v2. Se questo parametro non viene fornito, il nome del gateway v1 esistente verrà utilizzato con il suffisso *_v2* aggiunto.
   * **sslCertificates: [PSApplicationGatewaySslCertificate]: facoltativo**.  Un elenco delimitato da virgole di oggetti PSApplicationGatewaySslCertificate creati per rappresentare i certificati SSL dal gateway v1 deve essere caricato nel nuovo gateway v2. Per ognuno dei certificati SSL configurati per il gateway Standard v1 o WAF v1, `New-AzApplicationGatewaySslCertificate` è possibile creare un nuovo oggetto PSApplicationGatewaySslCertificate tramite il comando illustrato di seguito. È necessario il percorso del file del certificato SSL e la password.

     Questo parametro è facoltativo solo se non si dispone di listener HTTPS configurati per il gateway v1 o WAF. Se si dispone di almeno una configurazione del listener HTTPS, è necessario specificare questo parametro.

      ```azurepowershell  
      $password = ConvertTo-SecureString <cert-password> -AsPlainText -Force
      $mySslCert1 = New-AzApplicationGatewaySslCertificate -Name "Cert01" `
        -CertificateFile <Cert-File-Path-1> `
        -Password $password 
      $mySslCert2 = New-AzApplicationGatewaySslCertificate -Name "Cert02" `
        -CertificateFile <Cert-File-Path-2> `
        -Password $password
      ```

     È possibile `$mySslCert1, $mySslCert2` passare (separati da virgole) nell'esempio precedente come valori per questo parametro nello script.
   * **trustedRootCertificates: [PSApplicationGatewayTrustedRootCertificate]: facoltativo**. Elenco delimitato da virgole di oggetti PSApplicationGatewayTrustedRootCertificate creati per rappresentare i [certificati radice attendibili](ssl-overview.md) per l'autenticazione delle istanze back-end dal gateway v2.
   
      ```azurepowershell
      $certFilePath = ".\rootCA.cer"
      $trustedCert = New-AzApplicationGatewayTrustedRootCertificate -Name "trustedCert1" -CertificateFile $certFilePath
      ```

      Per creare un elenco di oggetti PSApplicationGatewayTrustedRootCertificate, vedere [New-AzApplicationGatewayTrustedRootCertificate](https://docs.microsoft.com/powershell/module/Az.Network/New-AzApplicationGatewayTrustedRootCertificate?view=azps-2.1.0&viewFallbackFrom=azps-2.0.0).
   * **privateIpAddress: [Stringa]: Facoltativo**. Un indirizzo IP privato specifico che si desidera associare al nuovo gateway v2.  Deve essere dalla stessa rete virtuale allocata per il nuovo gateway v2. Se non viene specificato, lo script alloca un indirizzo IP privato per il gateway v2.
   * **publicIpResourceId: [Stringa]: Facoltativo**. ResourceId della risorsa indirizzo IP pubblico (SKU standard) esistente nella sottoscrizione che si vuole allocare al nuovo gateway v2. Se non viene specificato, lo script alloca un nuovo indirizzo IP pubblico nello stesso gruppo di risorse. Il nome è il nome del gateway v2 con *-IP* aggiunto.
   * **validateMigration: [switch]: Facoltativo**. Utilizzare questo parametro se si desidera che lo script eseda alcune convalide di confronto della configurazione di base dopo la creazione del gateway v2 e la copia della configurazione. Per impostazione predefinita, non viene eseguita alcuna convalida.
   * **enableAutoScale: [switch]: Facoltativo**. Utilizzare questo parametro se si desidera che lo script abiliti la scalabilità automatica nel nuovo gateway v2 dopo che è stato creato. Per impostazione predefinita, AutoScaling è disabilitato. È sempre possibile abilitarlo manualmente in un secondo momento sul gateway v2 appena creato.

1. Eseguire lo script utilizzando i parametri appropriati. La finitura potrebbe richiedere da cinque a sette minuti.

    **Esempio**

   ```azurepowershell
   AzureAppGWMigration.ps1 `
      -resourceId /subscriptions/8b1d0fea-8d57-4975-adfb-308f1f4d12aa/resourceGroups/MyResourceGroup/providers/Microsoft.Network/applicationGateways/myv1appgateway `
      -subnetAddressRange 10.0.0.0/24 `
      -appgwname "MynewV2gw" `
      -sslCertificates $mySslCert1,$mySslCert2 `
      -trustedRootCertificates $trustedCert `
      -privateIpAddress "10.0.0.1" `
      -publicIpResourceId "/subscriptions/8b1d0fea-8d57-4975-adfb-308f1f4d12aa/resourceGroups/MyResourceGroup/providers/Microsoft.Network/publicIPAddresses/MyPublicIP" `
      -validateMigration -enableAutoScale
   ```

## <a name="migrate-client-traffic"></a>Eseguire la migrazione del traffico clientMigrate client traffic

Verificare innanzitutto che lo script abbia creato correttamente un nuovo gateway v2 con la configurazione esatta migrata dal gateway v1. È possibile verificarlo dal portale di Azure.You can verify this from the Azure portal.

Inoltre, inviare una piccola quantità di traffico attraverso il gateway v2 come test manuale.
  
Ecco alcuni scenari in cui il gateway applicazione corrente (Standard) può ricevere traffico client e i nostri consigli per ognuno di essi:Here are a few scenarios where your current application gateway (Standard) may receive client traffic, and our recommendations for each each:

* **Una zona DNS personalizzata ( ad esempio, contoso.com) che punta all'indirizzo IP front-end (utilizzando un record A) associato al gateway Standard v1 o WAF v1.**

    È possibile aggiornare il record DNS in modo che punti all'IP front-end o all'etichetta DNS associata al gateway applicazione Standard_v2. A seconda del valore TTL configurato nel record DNS, la migrazione del traffico client al nuovo gateway v2 potrebbe richiedere del tempo.
* **Una zona DNS personalizzata, ad esempio contoso.com, punta all'etichetta DNS (ad esempio, *myappgw.eastus.cloudapp.azure.com* utilizzando un record CNAME) associata al gateway v1.**

   Sono disponibili due opzioni:

  * Se si usano indirizzi IP pubblici nel gateway applicazione, è possibile eseguire una migrazione controllata e granulare usando un profilo di Gestione traffico per instradare il traffico in modo incrementale (metodo di routing del traffico ponderato) al nuovo gateway v2.

    A tale scopo, `www.contoso.com`è possibile aggiungere le etichette DNS dei gateway applicazione v1 e v2 al profilo di Gestione [traffico](../traffic-manager/traffic-manager-routing-methods.md#weighted-traffic-routing-method)e il record DNS personalizzato , ad esempio , al dominio di Gestione traffico, ad esempio contoso.trafficmanager.net.
  * In alternativa, è possibile aggiornare il record DNS del dominio personalizzato in modo che punti all'etichetta DNS del nuovo gateway applicazione v2. A seconda del valore TTL configurato nel record DNS, la migrazione del traffico client al nuovo gateway v2 potrebbe richiedere del tempo.
* **I client si connettono all'indirizzo IP front-end del gateway applicazione.**

   Aggiornare i client in modo che utilizzino gli indirizzi IP associati al gateway applicazione v2 appena creato. Si consiglia di non utilizzare direttamente gli indirizzi IP. Valutare la possibilità di utilizzare l'etichetta del nome DNS (ad esempio, yourgateway.eastus.cloudapp.azure.com) associata al gateway applicazione per la creazione di CNAME per la propria zona DNS personalizzata, ad esempio contoso.com.

## <a name="common-questions"></a>Domande frequenti

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Esistono limitazioni con lo script di Azure PowerShell per eseguire la migrazione della configurazione dalla versione 1 alla versione 2?

Sì. Vedere [Avvertenze/Limitazioni](#caveatslimitations).

### <a name="is-this-article-and-the-azure-powershell-script-applicable-for-application-gateway-waf-product-as-well"></a>Questo articolo e lo script di Azure PowerShell sono applicabili anche per il prodotto WAF del gateway applicazione? 

Sì.

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-v1-gateway-to-the-newly-created-v2-gateway"></a>Lo script di Azure PowerShell passa anche il traffico dal gateway v1 al gateway v2 appena creato?

No. Lo script di Azure PowerShell esegue solo la migrazione della configurazione. La migrazione effettiva del traffico è responsabilità dell'utente e del controllo dell'utente.

### <a name="is-the-new-v2-gateway-created-by-the-azure-powershell-script-sized-appropriately-to-handle-all-of-the-traffic-that-is-currently-served-by-my-v1-gateway"></a>Il nuovo gateway v2 creato dallo script di Azure PowerShell è dimensionato in modo appropriato per gestire tutto il traffico attualmente servito dal gateway v1?

Lo script di Azure PowerShell crea un nuovo gateway v2 con una dimensione appropriata per gestire il traffico nel gateway v1 esistente. La scalabilità automatica è disabilitata per impostazione predefinita, ma è possibile abilitare la scalabilità automatica quando si esegue lo script.

### <a name="i-configured-my-v1-gateway--to-send-logs-to-azure-storage-does-the-script-replicate-this-configuration-for-v2-as-well"></a>Ho configurato il gateway v1 per inviare i log all'archiviazione di Azure.I configured my v1 gateway to send logs to Azure storage. Lo script replica questa configurazione anche per la versione 2?

No. Lo script non replica questa configurazione per la versione 2. È necessario aggiungere la configurazione del log separatamente al gateway v2 migrato.

### <a name="does-this-script-support-certificates-uploaded-to-azure-keyvault-"></a>Questo script supporta i certificati caricati in Azure KeyVault?

No. Attualmente lo script non supporta i certificati in KeyVault. Tuttavia, questo viene considerato per una versione futura.

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>Mi sono imbattuto in alcuni problemi con l'utilizzo di questo script. Come posso ottenere aiuto?
  
È possibile inviare appgwmigrationsup@microsoft.comun messaggio di posta elettronica a , aprire un caso di supporto con il supporto di Azure o eseguire entrambe le operazione.

## <a name="next-steps"></a>Passaggi successivi

[Informazioni sul gateway applicazione v2](application-gateway-autoscaling-zone-redundant.md)
