---
title: Eseguire la migrazione di applicazione Azure gateway e del Web Application Firewall da V1 a V2
description: Questo articolo illustra come eseguire la migrazione di applicazione Azure gateway e del Web Application Firewall da V1 a V2
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 08/10/2019
ms.author: victorh
ms.openlocfilehash: e32443e01e8b44ff5a891afc76378a53b13d7ddd
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73833310"
---
# <a name="migrate-azure-application-gateway-and-web-application-firewall-from-v1-to-v2"></a>Eseguire la migrazione di applicazione Azure gateway e del Web Application Firewall da V1 a V2

È ora disponibile [applicazione Azure gateway e Web Application Firewall (WAF) V2](application-gateway-autoscaling-zone-redundant.md) , che offre funzionalità aggiuntive, ad esempio la scalabilità automatica e la ridondanza della zona di disponibilità. Tuttavia, i gateway versione 1 esistenti non vengono aggiornati automaticamente alla versione 2. Se si desidera eseguire la migrazione da V1 a V2, attenersi alla procedura descritta in questo articolo.

La migrazione prevede due fasi:

1. Eseguire la migrazione della configurazione
2. Migrare il traffico client

Questo articolo illustra la migrazione della configurazione. La migrazione del traffico client varia a seconda dell'ambiente specifico. Tuttavia, [vengono fornite](#migrate-client-traffic)alcune raccomandazioni generali di alto livello.

## <a name="migration-overview"></a>Panoramica della migrazione

È disponibile uno script Azure PowerShell che esegue le operazioni seguenti:

* Crea una nuova Standard_v2 o WAF_v2 gateway in una subnet di rete virtuale specificata.
* Copia senza difficoltà la configurazione associata al gateway standard V1 o WAF al nuovo Standard_V2 o WAF_V2 gateway.

### <a name="caveatslimitations"></a>Caveats\Limitations

* Il nuovo gateway V2 dispone di nuovi indirizzi IP pubblici e privati. Non è possibile spostare facilmente gli indirizzi IP associati al gateway V1 esistente alla versione V2. Tuttavia, è possibile allocare un indirizzo IP pubblico o privato esistente (non allocato) al nuovo gateway V2.
* È necessario fornire uno spazio di indirizzi IP per un'altra subnet all'interno della rete virtuale in cui si trova il gateway V1. Lo script non è in grado di creare il gateway V2 in tutte le subnet esistenti che dispongono già di un gateway V1. Tuttavia, se la subnet esistente dispone già di un gateway V2, questo potrebbe continuare a funzionare purché lo spazio degli indirizzi IP sia sufficiente.
* Per eseguire la migrazione di una configurazione SSL, è necessario specificare tutti i certificati SSL usati nel gateway V1.
* Se è abilitata la modalità FIPS per il gateway V1, non verrà eseguita la migrazione al nuovo gateway V2. La modalità FIPS non è supportata nella versione V2.
* V2 non supporta IPv6, quindi non viene eseguita la migrazione dei gateway V1 abilitati per IPv6. Se si esegue lo script, l'operazione potrebbe non essere completata.
* Se il gateway V1 ha solo un indirizzo IP privato, lo script crea un indirizzo IP pubblico e un indirizzo IP privato per il nuovo gateway V2. i gateway V2 attualmente non supportano solo indirizzi IP privati.

## <a name="download-the-script"></a>Scaricare lo script

Scaricare lo script di migrazione dalla [PowerShell Gallery](https://www.powershellgallery.com/packages/AzureAppGWMigration).

## <a name="use-the-script"></a>USA lo script

Sono disponibili due opzioni a seconda della configurazione e delle preferenze dell'ambiente di PowerShell locale:

* Se non si dispone di Azure AZ Modules installato o non si vuole disinstallare i moduli AZ di Azure, l'opzione migliore consiste nell'usare l'opzione `Install-Script` per eseguire lo script.
* Se è necessario proteggere i moduli di Azure AZ, la scommessa migliore consiste nel scaricare lo script ed eseguirlo direttamente.

Per determinare se Azure AZ Modules è installato, eseguire `Get-InstalledModule -Name az`. Se non vengono visualizzati i moduli AZ installati, è possibile usare il metodo `Install-Script`.

### <a name="install-using-the-install-script-method"></a>Eseguire l'installazione usando il metodo Install-script

Per usare questa opzione, non è necessario che nel computer siano installati i moduli AZ di Azure. Se sono installati, il comando seguente visualizza un errore. È possibile disinstallare i moduli di Azure AZ oppure usare l'altra opzione per scaricare lo script manualmente ed eseguirlo.
  
Eseguire lo script con il comando seguente:

`Install-Script -Name AzureAppGWMigration`

Questo comando installa anche i moduli AZ richiesti.  

### <a name="install-using-the-script-directly"></a>Installare usando direttamente lo script

Se si dispone di alcuni moduli AZ di Azure installati e non è possibile disinstallarli (o non si vuole disinstallarli), è possibile scaricare manualmente lo script usando la scheda **download manuale** nel collegamento per il download dello script. Lo script viene scaricato come file nupkg non elaborato. Per installare lo script da questo file nupkg, vedere [download manuale del pacchetto](https://docs.microsoft.com/powershell/gallery/how-to/working-with-packages/manual-download).

Per eseguire lo script:

1. Usare `Connect-AzAccount` per connettersi ad Azure.

1. Usare `Import-Module Az` per importare i moduli AZ.

1. Eseguire `Get-Help AzureAppGWMigration.ps1` per esaminare i parametri obbligatori:

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
   * **resourceId: [String]: required** . si tratta dell'ID risorsa di Azure per il gateway standard V1 o WAF V1 esistente. Per trovare questo valore stringa, passare alla portale di Azure, selezionare il gateway applicazione o la risorsa WAF e fare clic sul collegamento **Properties (proprietà** ) per il gateway. L'ID risorsa si trova nella pagina.

     È anche possibile eseguire i comandi di Azure PowerShell seguenti per ottenere l'ID risorsa:

     ```azurepowershell
     $appgw = Get-AzApplicationGateway -Name <v1 gateway name> -ResourceGroupName <resource group Name> 
     $appgw.Id
     ```

   * **subnetAddressRange: [String]: required** . si tratta dello spazio degli indirizzi IP allocato (o che si desidera allocare) per una nuova subnet che contiene il nuovo gateway V2. Questa operazione deve essere specificata nella notazione CIDR. Ad esempio: 10.0.0.0/24. Non è necessario creare questa subnet in anticipo. Lo script lo crea automaticamente se non esiste.
   * **appgwName: [String]: facoltativo**. Si tratta di una stringa specificata da usare come nome per il nuovo Standard_v2 o WAF_v2 gateway. Se questo parametro non viene specificato, il nome del gateway V1 esistente verrà usato con il suffisso *_v2* accodato.
   * **sslCertificates: [PSApplicationGatewaySslCertificate]: facoltativo**.  Un elenco delimitato da virgole di oggetti PSApplicationGatewaySslCertificate creati per rappresentare i certificati SSL dal gateway V1 deve essere caricato nel nuovo gateway V2. Per ognuno dei certificati SSL configurati per il gateway standard V1 o WAF V1, è possibile creare un nuovo oggetto PSApplicationGatewaySslCertificate tramite il comando `New-AzApplicationGatewaySslCertificate` riportato qui. È necessario il percorso del file del certificato SSL e la password.

       Questo parametro è facoltativo solo se non sono stati configurati listener HTTPS per il gateway V1 o WAF. Se è presente almeno una configurazione del listener HTTPS, è necessario specificare questo parametro.

      ```azurepowershell  
      $password = ConvertTo-SecureString <cert-password> -AsPlainText -Force
      $mySslCert1 = New-AzApplicationGatewaySslCertificate -Name "Cert01" `
        -CertificateFile <Cert-File-Path-1> `
        -Password $password 
      $mySslCert2 = New-AzApplicationGatewaySslCertificate -Name "Cert02" `
        -CertificateFile <Cert-File-Path-2> `
        -Password $password
      ```

      È possibile passare `$mySslCert1, $mySslCert2` (separate da virgola) nell'esempio precedente come valori per questo parametro nello script.
   * **trustedRootCertificates: [PSApplicationGatewayTrustedRootCertificate]: facoltativo**. Elenco delimitato da virgole di oggetti PSApplicationGatewayTrustedRootCertificate creati per rappresentare i [certificati radice attendibili](ssl-overview.md) per l'autenticazione delle istanze di back-end dal gateway V2.  

      Per creare un elenco di oggetti PSApplicationGatewayTrustedRootCertificate, vedere [New-AzApplicationGatewayTrustedRootCertificate](https://docs.microsoft.com/powershell/module/Az.Network/New-AzApplicationGatewayTrustedRootCertificate?view=azps-2.1.0&viewFallbackFrom=azps-2.0.0).
   * **privateIpAddress: [String]: facoltativo**. Un indirizzo IP privato specifico che si vuole associare al nuovo gateway V2.  Deve provenire dallo stesso VNet allocato per il nuovo gateway V2. Se non è specificato, lo script alloca un indirizzo IP privato per il gateway V2.
    * **publicIpResourceId: [String]: facoltativo**. ResourceId di una risorsa di indirizzo IP pubblico (SKU standard) nella sottoscrizione che si vuole allocare al nuovo gateway V2. Se non è specificato, lo script alloca un nuovo indirizzo IP pubblico nello stesso gruppo di risorse. Il nome è il nome del gateway V2 con *-IP* aggiunto.
   * **validateMigration: [switch]: facoltativo**. Usare questo parametro se si vuole che lo script esegua alcune convalide di confronto di configurazione di base dopo la creazione del gateway V2 e la copia di configurazione. Per impostazione predefinita, non viene eseguita alcuna convalida.
   * **enableAutoScale: [switch]: facoltativo**. Usare questo parametro se si vuole che lo script abiliti la scalabilità automatica sul nuovo gateway v2 dopo che è stato creato. Per impostazione predefinita, la scalabilità automatica è disabilitata. È sempre possibile abilitarlo manualmente in un secondo momento nel gateway V2 appena creato.

1. Eseguire lo script usando i parametri appropriati. Il completamento può richiedere da cinque a sette minuti.

    **Esempio**

   ```azurepowershell
   AzureAppGWMigration.ps1 `
      -resourceId /subscriptions/8b1d0fea-8d57-4975-adfb-308f1f4d12aa/resourceGroups/MyResourceGroup/providers/Microsoft.Network/applicationGateways/myv1appgateway `
      -subnetAddressRange 10.0.0.0/24 `
      -appgwname "MynewV2gw" `
      -sslCertificates $Certs `
      -trustedRootCertificates $trustedCert `
      -privateIpAddress "10.0.0.1" `
      -publicIpResourceId "MyPublicIP" `
      -validateMigration -enableAutoScale
   ```

## <a name="migrate-client-traffic"></a>Migrare il traffico client

Verificare prima di tutto che lo script abbia creato correttamente un nuovo gateway V2 con la configurazione esatta migrata dal gateway V1. È possibile verificarlo dalla portale di Azure.

Inviare inoltre una piccola quantità di traffico attraverso il gateway v2 come test manuale.
  
Di seguito sono riportati alcuni scenari in cui il gateway applicazione corrente (standard) può ricevere il traffico client e i suggerimenti per ognuno di essi:

* **Una zona DNS personalizzata (ad esempio, contoso.com) che punta all'indirizzo IP front-end (usando un record A) associato al gateway standard V1 o WAF V1**.

    È possibile aggiornare il record DNS in modo che punti all'indirizzo IP front-end o all'etichetta DNS associata al gateway applicazione Standard_v2. A seconda della durata (TTL) configurata nel record DNS, potrebbe essere necessario un po' di tempo per eseguire la migrazione di tutto il traffico client al nuovo gateway V2.
* **Una zona DNS personalizzata (ad esempio, contoso.com) che punta all'etichetta DNS (ad esempio: *myappgw.eastus.cloudapp.Azure.com* usando un record CNAME) associata al gateway V1**.

   Sono disponibili due opzioni:

  * Se si usano indirizzi IP pubblici nel gateway applicazione, è possibile eseguire una migrazione controllata e granulare usando un profilo di gestione traffico per instradare in modo incrementale il traffico (metodo di routing del traffico ponderato) al nuovo gateway V2.

    A tale scopo, aggiungere le etichette DNS dei gateway applicazione V1 e V2 al [profilo di gestione traffico](../traffic-manager/traffic-manager-routing-methods.md#weighted-traffic-routing-method)e il record DNS personalizzato (ad esempio, `www.contoso.com`) al dominio di Traffic Manager (ad esempio, contoso.trafficmanager.NET).
  * In alternativa, è possibile aggiornare il record DNS di dominio personalizzato in modo che punti all'etichetta DNS del nuovo gateway applicazione V2. A seconda della durata (TTL) configurata nel record DNS, potrebbe essere necessario un po' di tempo per eseguire la migrazione di tutto il traffico client al nuovo gateway V2.
* I **client si connettono all'indirizzo IP front-end del gateway applicazione**.

   Aggiornare i client per usare gli indirizzi IP associati al gateway applicazione V2 appena creato. Si consiglia di non usare direttamente gli indirizzi IP. Si consiglia di usare l'etichetta del nome DNS (ad esempio, yourgateway.eastus.cloudapp.azure.com) associata al gateway applicazione che è possibile CNAME alla propria zona DNS personalizzata (ad esempio, contoso.com).

## <a name="common-questions"></a>Domande frequenti

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Esistono limitazioni allo script Azure PowerShell per eseguire la migrazione della configurazione dalla versione V1 alla versione V2?

Sì. Vedere [avvertenze/limitazioni](#caveatslimitations).

### <a name="is-this-article-and-the-azure-powershell-script-applicable-for-application-gateway-waf-product-as-well"></a>Questo articolo e lo script Azure PowerShell applicabili anche al prodotto WAF del gateway applicazione? 

Sì.

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-v1-gateway-to-the-newly-created-v2-gateway"></a>Lo script di Azure PowerShell passa anche il traffico dal gateway V1 al gateway V2 appena creato?

No. Lo script Azure PowerShell esegue solo la migrazione della configurazione. La migrazione effettiva del traffico è responsabilità dell'utente e del controllo.

### <a name="is-the-new-v2-gateway-created-by-the-azure-powershell-script-sized-appropriately-to-handle-all-of-the-traffic-that-is-currently-served-by-my-v1-gateway"></a>Il nuovo gateway V2 creato dallo script Azure PowerShell dimensionato in modo appropriato per gestire tutto il traffico attualmente servito dal gateway V1?

Lo script Azure PowerShell crea un nuovo gateway V2 con le dimensioni appropriate per gestire il traffico nel gateway V1 esistente. La scalabilità automatica è disabilitata per impostazione predefinita, ma è possibile abilitare la scalabilità automatica durante l'esecuzione dello script.

### <a name="i-configured-my-v1-gateway--to-send-logs-to-azure-storage-does-the-script-replicate-this-configuration-for-v2-as-well"></a>Ho configurato il gateway V1 per inviare i log ad archiviazione di Azure. Lo script replica anche questa configurazione per V2?

No. Lo script non replica questa configurazione per V2. È necessario aggiungere la configurazione del log separatamente al gateway V2 migrato.

### <a name="does-this-script-support-certificates-uploaded-to-azure-keyvault-"></a>Questo script supporta i certificati caricati nell'insieme di credenziali delle credenziali di Azure?

No. Attualmente lo script non supporta i certificati nell'insieme di credenziali delle credenziali. Questa operazione viene tuttavia considerata per una versione futura.

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>Si sono verificati alcuni problemi con l'uso di questo script. Come è possibile ottenere assistenza?
  
È possibile inviare un messaggio di posta elettronica a appgwmigrationsup@microsoft.com, aprire un caso di supporto con il supporto tecnico di Azure o eseguire entrambe le operazioni.

## <a name="next-steps"></a>Passaggi successivi

[Informazioni sul gateway applicazione V2](application-gateway-autoscaling-zone-redundant.md)
