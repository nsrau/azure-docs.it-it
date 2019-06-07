---
title: Eseguire la migrazione di Gateway applicazione di Azure e Web Application Firewall da v1 a v2
description: Questo articolo illustra come eseguire la migrazione di Gateway applicazione di Azure e il Web Application Firewall da v1 a v2
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 6/5/2019
ms.author: victorh
ms.openlocfilehash: 44d5ce3e194c873a564039934f518cb3a0e142e3
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66497171"
---
# <a name="migrate-azure-application-gateway-and-web-application-firewall-from-v1-to-v2"></a>Eseguire la migrazione di Gateway applicazione di Azure e Web Application Firewall da v1 a v2

[Gateway applicazione di Azure e Web Application Firewall (WAF) v2](application-gateway-autoscaling-zone-redundant.md) è ora disponibile, che offre funzionalità aggiuntive, ad esempio la ridondanza della zona di disponibilità e la scalabilità automatica. Tuttavia, i gateway esistenti v1 non sono aggiornati automaticamente allo v2. Se si desidera eseguire la migrazione da v1 a v2, seguire i passaggi descritti in questo articolo.

In una migrazione sono presenti due fasi:

1. La migrazione della configurazione
2. Eseguire la migrazione il traffico dei client

Questo articolo illustra la migrazione di configurazione. Migrazione di traffico client varia a seconda dell'ambiente specifico. Tuttavia, alcuni consigli generali, ad alto livello [forniti](#migrate-client-traffic).

## <a name="migration-overview"></a>Panoramica della migrazione

È disponibile uno script Azure PowerShell che esegue le operazioni seguenti:

* Crea un nuovo gateway Standard_v2 o WAF_v2 in una subnet di rete virtuale specificato.
* Consente di copiare facilmente la configurazione associata al gateway v1 Standard o WAF nel gateway Standard_V2 o WAF_V2 appena creato.

### <a name="caveatslimitations"></a>Caveats\Limitations

* Il nuovo gateway v2 ha nuovi indirizzi IP pubblici e privati. Non è possibile spostare gli indirizzi IP associati al gateway v1 esistente in modo trasparente a v2. Tuttavia, è possibile allocare un ovvero non allocato, pubblico o privato indirizzo IP esistente al nuovo gateway v2.
* È necessario fornire uno spazio di indirizzi IP per un'altra subnet nella rete virtuale in cui si trova il gateway v1. Lo script non è possibile creare il gateway v2 in qualsiasi subnet esistenti che già dispone di un gateway di v1. Tuttavia, se la subnet esistente ha già un gateway v2, che può ancora funzionare disponibile che è sufficiente spazio di indirizzi IP.
* Per eseguire la migrazione di una configurazione di SSL, è necessario specificare tutti i certificati SSL usati nel gateway v1.
* Se si dispone di modalità FIPS abilitata per il gateway di versione 1, non eseguire la migrazione al nuovo gateway v2. La modalità FIPS non è supportata nella versione 2.
* versione 2 non supporta IPv6, pertanto non vengono migrati i gateway v1 IPv6 abilitato. Se si esegue lo script, potrebbe non essere completata.
* Se il gateway v1 ha solo un indirizzo IP privato, lo script crea un indirizzo IP pubblico e un indirizzo IP privato per il nuovo gateway v2. i gateway v2 attualmente non supportano solo gli indirizzi IP privati.

## <a name="download-the-script"></a>Scaricare lo script

Scaricare lo script di migrazione dal [PowerShell Gallery](https://www.powershellgallery.com/packages/AzureAppGWMigration).

## <a name="use-the-script"></a>Usare lo script

Sono disponibili due opzioni per l'utente a seconda della configurazione dell'ambiente PowerShell locale e le preferenze:

* Se si non sono installati i moduli di Azure Az o si vuole disinstallare i moduli Azure Az, l'opzione migliore è utilizzare il `Install-Script` opzione per eseguire lo script.
* Se è necessario mantenere i moduli Azure Az, è il modo migliore per scaricare lo script ed eseguirlo direttamente.

Per determinare se sono installati i moduli di Azure Az, eseguire `Get-InstalledModule -Name az`. Se non viene visualizzato uno installato i moduli di Az, quindi è possibile usare il `Install-Script` (metodo).

### <a name="install-using-the-install-script-method"></a>Installare usando il metodo Install-Script

Per usare questa opzione, non sono necessari i moduli Azure Az installati nel computer. Se sono installati, il comando seguente visualizza un errore. È possibile disinstallare i moduli Azure Az o usare l'altra opzione per scaricare manualmente lo script ed eseguirlo.
  
Eseguire lo script con il comando seguente:

`Install-Script -Name AzureAppGWMigration`

Questo comando installa anche i moduli di Az necessari.  

### <a name="install-using-the-script-directly"></a>Installare usando lo script direttamente

Se si hanno alcuni moduli di Azure Az installati e non è possibile disinstallare tali elementi (o non si vuole disinstallarle), è possibile scaricare manualmente lo script usando il **Download manuale** scheda nel collegamento di download dello script. Lo script viene scaricato come file nupkg non elaborati. Per installare lo script dal file nupkg, vedere [Download di pacchetti manuale](https://docs.microsoft.com/powershell/gallery/how-to/working-with-packages/manual-download).

Per eseguire lo script:

1. Usare `Connect-AzAccount` per connettersi ad Azure.

1. Usare `Import-Module Az` per importare i moduli di Az.

1. Eseguire `Get-Help AzureAppGWMigration.ps1` per esaminare i parametri obbligatori:

   `AzureAppGwMigration.ps1
    -resourceId <v1 application gateway Resource ID>
    -subnetAddressRange <subnet space you want to use>
    -appgwName <string to use to append>
    -sslCertificates <comma-separated SSLCert objects as above>
    -trustedRootCertificates <comma-separated Trusted Root Cert objects as above>
    -privateIpAddress <private IP string>
    -publicIpResourceName <public IP name string>
    -validateMigration -enableAutoScale`

   Parametri per lo script:
   * **ID risorsa: [stringa]: Obbligatorio** -questa è l'ID di risorsa di Azure per il v1 Standard esistenti o gateway WAF v1. Per trovare questo valore di stringa, passare al portale di Azure, selezionare il gateway applicazione o la risorsa Web Application firewall e scegliere il **proprietà** collegamento per il gateway. L'ID risorsa si trova in tale pagina.

     È anche possibile eseguire i comandi di Azure PowerShell seguenti per ottenere l'ID risorsa:

     ```azurepowershell
     $appgw = Get-AzApplicationGateway -Name <v1 gateway name> -ResourceGroupName <resource group Name> 
     $appgw.Id
     ```

   * **subnetAddressRange: [stringa]:  Obbligatorio** -questo è lo spazio di indirizzi IP che è stato allocato (o da allocare) per una nuova subnet che contiene il nuovo gateway v2. Questo deve essere specificato nella notazione CIDR. Ad esempio:  10.0.0.0/24. Non devi creare questa subnet in anticipo. Lo script crea automaticamente se non esiste.
   * **appgwName: [stringa]: Facoltativo**. Questa è una stringa che specifica per l'uso come nome per il nuovo gateway Standard_v2 o WAF_v2. Se non viene specificato questo parametro, verrà utilizzato il nome del gateway di v1 esistente con il suffisso *_v2* aggiunto.
   * **sslCertificates: [PSApplicationGatewaySslCertificate]: Facoltativo**.  Un elenco delimitato da virgole di oggetti PSApplicationGatewaySslCertificate creata per rappresentare i certificati SSL dal gateway di versione 1 deve essere caricato per il nuovo gateway v2. Per ognuno dei certificati SSL configurati per la Standard v1 o gateway WAF v1, è possibile creare un nuovo oggetto PSApplicationGatewaySslCertificate tramite il `New-AzApplicationGatewaySslCertificate` riga di comando mostrata di seguito. È necessario il percorso del file di certificato SSL e la password.

       Questo parametro solo è facoltativo se non si dispone di listener HTTPS configurato per il gateway di v1 o di un WAF. Se si dispone di almeno una configurazione di listener HTTPS, è necessario specificare questo parametro.

      ```azurepowershell  
      $password = ConvertTo-SecureString <cert-password> -AsPlainText -Force
      $mySslCert1 = New-AzApplicationGatewaySslCertificate -Name "Cert01" `
        -CertificateFile <Cert-File-Path-1> `
        -Password $password 
      $mySslCert2 = New-AzApplicationGatewaySslCertificate -Name "Cert02" `
        -CertificateFile <Cert-File-Path-2> `
        -Password $password
      ```

      È possibile passare `$mySslCert1, $mySslCert2` (delimitato da virgole) nell'esempio precedente come valori per questo parametro nello script.
   * **trustedRootCertificates: [PSApplicationGatewayTrustedRootCertificate]: Facoltativo**. Un elenco delimitato da virgole di oggetti PSApplicationGatewayTrustedRootCertificate creata per rappresentare il [certificati radice Trusted](ssl-overview.md) per l'autenticazione delle istanze back-end dal gateway di v2.  

      Per creare un elenco di oggetti PSApplicationGatewayTrustedRootCertificate, vedere [New-AzApplicationGatewayTrustedRootCertificate](https://docs.microsoft.com/powershell/module/Az.Network/New-AzApplicationGatewayTrustedRootCertificate?view=azps-2.1.0&viewFallbackFrom=azps-2.0.0).
   * **privateIpAddress: [stringa]: Facoltativo**. Un indirizzo IP privato specifico che si desidera associare al nuovo gateway v2.  Questo deve essere compreso nella stessa rete virtuale che viene allocata per il nuovo gateway v2. Se non è specificato, lo script consente di allocare un indirizzo IP privato per il gateway v2.
    * **publicIpResourceId: [String]: Facoltativo**. Il valore di resourceId di una risorsa di indirizzo IP pubblico nella sottoscrizione che si desidera allocare per il nuovo gateway v2. Se non viene specificato, lo script consente di allocare un nuovo indirizzo IP pubblico nello stesso gruppo di risorse. Il nome è il nome del gateway v2 con *- IP* aggiunto.
   * **validateMigration: [switch]: Facoltativo**. Usare questo parametro se si desidera che lo script per eseguire alcune configurazioni di base delle convalide di confronto dopo la creazione del gateway v2 e la copia della configurazione. Per impostazione predefinita, viene eseguita alcuna convalida.
   * **enableAutoScale: [switch]: Facoltativo**. Usare questo parametro se si desidera che lo script per abilitare la scalabilità automatica nel nuovo gateway v2 dopo averlo creato. Per impostazione predefinita, la scalabilità automatica è disabilitata. È possibile sempre manualmente abilitarla in un secondo momento il gateway appena creata v2.

1. Eseguire lo script usando i parametri appropriati.

    **Esempio**

   ```azurepowershell
   AzureAppGWMigration.ps1 `
      -resourceId /subscriptions/8b1d0fea-8d57-4975-adfb-308f1f4d12aa/resourceGroups/MyResourceGroup/providers/Microsoft.Network/applicationGateways/myv1appgateway `
      -subnetAddressRange 10.0.0.0/24 `
      -appgwname "MynewV2gw" `
      -sslCertificates $Certs `
      -trustedRootCertificates $trustedCert `
      -privateIpAddress "10.0.0.1" `
      -publicIpResourceName "MyPublicIP" `
      -validateMigration -enableAutoScale
   ```

## <a name="migrate-client-traffic"></a>Eseguire la migrazione del traffico dei client

Innanzitutto, controllare che lo script è stato creato un nuovo gateway v2 con la configurazione esatta migrato dal gateway di v1. È possibile verificarlo dal portale di Azure.

Inoltre, inviare una piccola quantità di traffico attraverso il gateway di versione 2 come un test manuale.
  
Di seguito sono proposti alcuni scenari in cui il gateway applicazione corrente (Standard) può ricevere il traffico del client e le raccomandazioni per ciascuno di essi:

* **Una zona DNS personalizzata (ad esempio, contoso.com) che punta all'indirizzo IP front-end (utilizzando un record a) associato con v1 Standard o gateway WAF v1**.

    È possibile aggiornare il record DNS in modo che punti all'etichetta DNS o IP di front-end associato con il gateway applicazione Standard_v2. A seconda del valore TTL configurato nel record DNS potrebbe richiedere qualche minuto per tutto il traffico del client eseguire la migrazione al nuovo gateway v2.
* **Una zona DNS personalizzata (ad esempio, contoso.com) che punta all'etichetta DNS (ad esempio: *myappgw.eastus.cloudapp.azure.com* usando un record CNAME) associato con il gateway v1**.

   Sono disponibili due opzioni:

  * Se si usano indirizzi IP pubblici nel gateway applicazione, è possibile eseguire un controllata, migrazione granulare usando un profilo di gestione traffico per instradare in modo incrementale il traffico (metodo di routing del traffico ponderato) al nuovo gateway v2.

    È possibile farlo mediante l'aggiunta di etichette DNS del gateway applicazione di v1 e v2 per la [profilo di gestione traffico](../traffic-manager/traffic-manager-routing-methods.md#weighted-traffic-routing-method)e imposto il record DNS personalizzato (ad esempio, www.contoso.com) al dominio di Traffic Manager (ad esempio, il record CNAME Contoso.trafficmanager.NET).
  * In alternativa, è possibile aggiornare il record DNS di dominio personalizzato in modo che punti all'etichetta DNS del nuovo gateway applicazione v2. A seconda del valore TTL configurato nel record DNS potrebbe richiedere qualche minuto per tutto il traffico del client eseguire la migrazione al nuovo gateway v2.
* **I client si connettono al front-end di indirizzo IP del gateway applicazione**.

   Aggiornare il client per usare gli indirizzi IP associato al gateway applicazione v2 appena creato. È consigliabile non usare direttamente gli indirizzi IP. È consigliabile usare l'etichetta del nome DNS (ad esempio, yourgateway.eastus.cloudapp.azure.com) associato con il gateway applicazione che è possibile CNAME per la propria zona DNS personalizzata (ad esempio, contoso.com).

## <a name="common-questions"></a>Domande frequenti

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Sono presenti limitazioni con lo script Azure PowerShell per eseguire la migrazione della configurazione da v1 a v2?

Sì. Visualizzare [avvertenze e limitazioni](#caveatslimitations).

### <a name="is-this-article-and-the-azure-powershell-script-applicable-for-application-gateway-waf-product-as-well"></a>È applicabile per il prodotto di WAF del Gateway applicazione anche in questo articolo e lo script di PowerShell di Azure? 

Sì.

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-v1-gateway-to-the-newly-created-v2-gateway"></a>Lo script Azure PowerShell passa anche il traffico dal mio gateway v1 per il gateway appena creata v2?

No. Lo script di PowerShell di Azure solo la migrazione della configurazione. Migrazione effettivo del traffico è responsabilità dell'utente e nel controllo.

### <a name="is-the-new-v2-gateway-created-by-the-azure-powershell-script-sized-appropriately-to-handle-all-of-the-traffic-that-is-currently-served-by-my-v1-gateway"></a>Il nuovo gateway v2 creato dallo script di PowerShell di Azure appropriate per gestire tutto il traffico che attualmente viene servito dal mio gateway di versione 1?

Lo script Azure PowerShell crea un nuovo gateway v2 con una dimensione appropriata per gestire il traffico nel gateway V1 esistente. La scalabilità automatica è disabilitata per impostazione predefinita, ma è possibile abilitare la scalabilità automatica quando si esegue lo script.

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>Riscontrato alcuni problemi relativi all'uso di questo script. Come è possibile ottenere supporto?
  
È possibile inviare un messaggio di posta elettronica appgwmigrationsup@microsoft.com, aprire una richiesta di assistenza con supporto tecnico di Azure o eseguire entrambe le operazioni.

## <a name="next-steps"></a>Passaggi successivi

[Altre informazioni sull'applicazione Gateway v2](application-gateway-autoscaling-zone-redundant.md)
