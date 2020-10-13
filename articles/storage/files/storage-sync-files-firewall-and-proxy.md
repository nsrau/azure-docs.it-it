---
title: Impostazioni di proxy e firewall locali di Sincronizzazione file di Azure | Microsoft Docs
description: Informazioni sulle impostazioni di proxy e firewall locali Sincronizzazione file di Azure. Esaminare i dettagli di configurazione per le porte, le reti e le connessioni speciali ad Azure.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 06/24/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: e4f011d9286a0685f1b091b930155db969407423
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87903715"
---
# <a name="azure-file-sync-proxy-and-firewall-settings"></a>Impostazioni di proxy e firewall di Sincronizzazione file di Azure
Sincronizzazione file di Azure connette i server locali a File di Azure abilitando la sincronizzazione tra più siti e funzionalità di suddivisione in livelli cloud. È necessario quindi che un server locale sia connesso a Internet e che un amministratore IT scelga il percorso migliore per consentire al server di accedere ai servizi cloud di Azure.

Questo articolo offre informazioni dettagliate sui requisiti e le opzioni disponibili per connettere un server a Sincronizzazione file di Azure in modo sicuro ed efficiente.

Prima di seguire questa guida pratica, è consigliabile leggere [Considerazioni sulla rete per Sincronizzazione file di Azure](storage-sync-files-networking-overview.md).

## <a name="overview"></a>Panoramica
Sincronizzazione file di Azure svolge la funzione di servizio di orchestrazione tra Windows Server, la condivisione file di Azure e altri servizi di Azure per la sincronizzazione dei dati, come descritto nel gruppo di sincronizzazione. Per consentire il corretto funzionamento di Sincronizzazione file di Azure, è necessario configurare i server in modo che possano comunicare con i servizi di Azure seguenti:

- Archiviazione di Azure
- Sincronizzazione file di Azure
- Azure Resource Manager
- Servizi di autenticazione

> [!Note]  
> L'agente Sincronizzazione file di Azure in Windows Server avvia tutte le richieste ai servizi cloud ed è quindi necessario tenere conto del traffico in uscita solo dal punto di vista del firewall. <br /> Nessun servizio di Azure avvia una connessione all'agente Sincronizzazione file di Azure.

## <a name="ports"></a>Porte
Sincronizzazione file di Azure sposta i dati e i metadati dei file solo su connessioni HTTPS e richiede che la porta 443 sia aperta in uscita.
Tutto il traffico, quindi, viene crittografato.

## <a name="networks-and-special-connections-to-azure"></a>Reti e connessioni speciali ad Azure
L'agente Sincronizzazione file di Azure non presenta alcun requisito in merito a canali speciali come [ExpressRoute](../../expressroute/expressroute-introduction.md) e altri diretti ad Azure.

Sincronizzazione file di Azure interagisce con qualsiasi mezzo disponibile che consenta di accedere ad Azure adattandosi automaticamente alle varie caratteristiche di rete, come la latenza e la larghezza di banda, e offrendo il controllo amministrativo per l'ottimizzazione. Non sono ancora disponibili tutte le funzionalità. Per configurare un comportamento specifico, è possibile usare [UserVoice per File di Azure](https://feedback.azure.com/forums/217298-storage?category_id=180670).

## <a name="proxy"></a>Proxy
Sincronizzazione file di Azure supporta impostazioni proxy a livello di computer specifiche per l'app.

**Le impostazioni proxy specifiche dell'app** consentono la configurazione di un proxy specifico per il traffico sincronizzazione file di Azure. Le impostazioni proxy specifiche per le app sono supportate nella versione 4.0.1.0 o versioni successive dell'agente e possono essere configurate durante l'installazione dell'agente oppure usando il cmdlet Set-StorageSyncProxyConfiguration di PowerShell.

Comandi di PowerShell per configurare le impostazioni proxy specifiche dell'app:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Set-StorageSyncProxyConfiguration -Address <url> -Port <port number> -ProxyCredential <credentials>
```
Le **impostazioni proxy a livello di computer** sono trasparenti per l'agente sincronizzazione file di Azure perché l'intero traffico del server viene instradato attraverso il proxy.

Per configurare le impostazioni proxy a livello di computer, attenersi alla procedura seguente: 

1. Configurare le impostazioni proxy per le applicazioni .NET 

   - Modificare questi due file:  
     C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config  
     C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config\machine.config

   - Aggiungere la sezione <system.net> nel file machine.config (sotto la sezione <system.serviceModel>).  Modificare 127.0.01:8888 per l'indirizzo IP e la porta del server proxy. 
     ```
      <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
          <proxy autoDetect="false" bypassonlocal="false" proxyaddress="http://127.0.0.1:8888" usesystemdefault="false" />
        </defaultProxy>
      </system.net>
     ```

2. Configurare le impostazioni del proxy WinHTTP 

   - Eseguire il comando seguente da un prompt dei comandi con privilegi elevati o PowerShell per visualizzare le impostazioni del proxy esistente:   

     Visualizzare il proxy netsh winhttp

   - Eseguire il comando seguente da un prompt dei comandi con privilegi elevati o PowerShell per impostare le impostazioni del proxy (modificare 127.0.01:8888 per l'indirizzo IP e la porta del server proxy):  

     Impostare il proxy netsh winhttp 127.0.0.1:8888

3. Riavviare il servizio Storage Sync Agent eseguendo il comando seguente da un prompt dei comandi con privilegi elevati o da PowerShell: 

      net stop filesyncsvc

      Nota: il servizio Storage Sync Agent (filesyncsvc) si avvia in modo automatico dopo l'arresto.

## <a name="firewall"></a>Firewall
Come indicato in una sezione precedente, la porta 443 deve essere aperta in uscita. In base ai criteri definiti a livello di data center, ramo o area geografica, è possibile che sia necessario o preferibile limitare il traffico su questa porta a domini specifici.

La tabella seguente illustra i domini necessari per la comunicazione:

| Servizio | Endpoint cloud pubblico | Endpoint di Azure per enti pubblici | Utilizzo |
|---------|----------------|---------------|------------------------------|
| **Azure Resource Manager** | `https://management.azure.com` | https://management.usgovcloudapi.net | Qualsiasi chiamata utente (ad esempio, PowerShell) giunge o passa attraverso questo URL, inclusa la chiamata di registrazione iniziale del server. |
| **Azure Active Directory** | https://login.windows.net<br>`https://login.microsoftonline.com` | https://login.microsoftonline.us | Le chiamate di Azure Resource Manager devono essere effettuate da un utente autenticato. Per l'autenticazione utente viene usato questo URL. |
| **Azure Active Directory** | https://graph.microsoft.com/ | https://graph.microsoft.com/ | Nel corso del processo di distribuzione di Sincronizzazione file di Azure verrà creata un'entità servizio nella sessione di Azure Active Directory associata alla sottoscrizione. A tale scopo viene usato questo URL. Questa entità viene usata per delegare una quantità minima di diritti al servizio Sincronizzazione file di Azure. L'utente che esegue la configurazione iniziale di Sincronizzazione file di Azure deve essere un utente autenticato con privilegi di proprietario della sottoscrizione. |
| **Azure Active Directory** | https://secure.aadcdn.microsoftonline-p.com | Usare l'URL dell'endpoint pubblico. | Questo URL è accessibile dalla libreria di autenticazione Active Directory utilizzata dall'interfaccia utente di registrazione Sincronizzazione file di Azure server per accedere all'amministratore. |
| **Archiviazione di Azure** | &ast;.core.windows.net | &ast;. core.usgovcloudapi.net | Quando il server scarica un file, esegue lo spostamento dati in modo più efficiente se comunica direttamente con la condivisione file di Azure nell'account di archiviazione. Il server ha una chiave di firma di accesso condiviso che consente l'accesso solo a specifiche condivisioni file. |
| **Sincronizzazione file di Azure** | &ast;.one.microsoft.com<br>&ast;. afs.azure.net | &ast;. afs.azure.us | Dopo la registrazione iniziale, il server riceve un URL regionale relativo all'istanza del servizio Sincronizzazione file di Azure disponibile in quell'area. Il server può usare l'URL per comunicare direttamente e in modo efficiente con l'istanza che gestisce la sincronizzazione. |
| **Infrastruttura a chiave pubblica Microsoft** | https://www.microsoft.com/pki/mscorp/cps<br><http://ocsp.msocsp.com> | https://www.microsoft.com/pki/mscorp/cps<br><http://ocsp.msocsp.com> | Dopo l'installazione dell'agente di Sincronizzazione file di Azure, l'URL dell'infrastruttura a chiave pubblica viene usato per scaricare i certificati intermedi necessari per comunicare con il servizio Sincronizzazione file di Azure e la condivisione file di Azure. L'URL OCSP viene usato per controllare lo stato di un certificato. |

> [!Important]
> Quando si consente il traffico a &ast; . AFS.Azure.NET, il traffico è possibile solo per il servizio di sincronizzazione. Non sono presenti altri servizi Microsoft che usano questo dominio.
> Quando si consente il traffico a &ast;.one.microsoft.com, il traffico dal server non è più limitato solo al servizio di sincronizzazione. Nei sottodomini sono infatti disponibili molti altri servizi Microsoft.

Se &ast; . AFS.Azure.NET o &ast; . One.Microsoft.com è troppo ampio, è possibile limitare la comunicazione del server consentendo la comunicazione solo con istanze internazionali esplicite del servizio file di Azure Sync. Le istanze da scegliere dipendono dall'area del servizio di sincronizzazione archiviazione in cui è stato distribuito e registrato il server. Tale area è chiamata "URL dell'endpoint primario" nella tabella seguente.

Per ragioni di continuità aziendale e ripristino di emergenza (BCDR) è possibile che siano state specificate condivisioni file di Azure in un account di archiviazione con ridondanza geografica (GRS). In tal caso, le condivisioni file di Azure eseguiranno il failover nell'area abbinata se si verifica un'interruzione duratura a livello di area. Sincronizzazione file di Azure usa le stesse associazioni a livello di area della risorsa di archiviazione. Quindi, se si usano gli account di archiviazione GRS, è necessario abilitare URL aggiuntivi per consentire al server di comunicare con l'area abbinata per Sincronizzazione file di Azure. La tabella seguente chiama questa "area abbinata". Inoltre, vi è anche un URL del profilo di gestione traffico che deve essere abilitato. In questo modo il traffico di rete può essere indirizzato di nuovo con facilità all'area abbinata in caso di failover ed è chiamato "URL di individuazione" nella tabella seguente.

| Cloud  | Region | URL dell'endpoint primario | Area associata | URL di individuazione |
|--------|--------|----------------------|---------------|---------------|
| Pubblico |Australia orientale | https: \/ /australiaeast01.AFS.Azure.NET<br>https: \/ /Kailani-Aue.One.Microsoft.com | Australia sud-orientale | https: \/ /TM-australiaeast01.AFS.Azure.NET<br>https: \/ /TM-Kailani-Aue.One.Microsoft.com |
| Pubblico |Australia sud-orientale | https: \/ /australiasoutheast01.AFS.Azure.NET<br>https: \/ /Kailani-aus.One.Microsoft.com | Australia orientale | https: \/ /TM-australiasoutheast01.AFS.Azure.NET<br>https: \/ /TM-Kailani-aus.One.Microsoft.com |
| Pubblico | Brasile meridionale | https: \/ /brazilsouth01.AFS.Azure.NET | Stati Uniti centro-meridionali | https: \/ /TM-brazilsouth01.AFS.Azure.NET |
| Pubblico | Canada centrale | https: \/ /canadacentral01.AFS.Azure.NET<br>https: \/ /Kailani-CAC.One.Microsoft.com | Canada orientale | https: \/ /TM-canadacentral01.AFS.Azure.NET<br>https: \/ /TM-Kailani-CAC.One.Microsoft.com |
| Pubblico | Canada orientale | https: \/ /canadaeast01.AFS.Azure.NET<br>https: \/ /Kailani-CAE.One.Microsoft.com | Canada centrale | https: \/ /TM-canadaeast01.AFS.Azure.NET<br>https: \/ /TM-Kailani.CAE.One.Microsoft.com |
| Pubblico | Central India | https: \/ /centralindia01.AFS.Azure.NET<br>https: \/ /Kailani-cin.One.Microsoft.com | India meridionale | https: \/ /TM-centralindia01.AFS.Azure.NET<br>https: \/ /TM-Kailani-cin.One.Microsoft.com |
| Pubblico | Stati Uniti centrali | https: \/ /centralus01.AFS.Azure.NET<br>https: \/ /Kailani-CUS.One.Microsoft.com | Stati Uniti orientali 2 | https: \/ /TM-centralus01.AFS.Azure.NET<br>https: \/ /TM-Kailani-CUS.One.Microsoft.com |
| Pubblico | Asia orientale | https: \/ /eastasia01.AFS.Azure.NET<br>https: \/ /kailani11.One.Microsoft.com | Asia sud-orientale | https: \/ /TM-eastasia01.AFS.Azure.NET<br>https: \/ /TM-kailani11.One.Microsoft.com |
| Pubblico | Stati Uniti orientali | https: \/ /eastus01.AFS.Azure.NET<br>https: \/ /kailani1.One.Microsoft.com | Stati Uniti Occidentali | https: \/ /TM-eastus01.AFS.Azure.NET<br>https: \/ /TM-kailani1.One.Microsoft.com |
| Pubblico | Stati Uniti orientali 2 | https: \/ /eastus201.AFS.Azure.NET<br>https: \/ /Kailani-ESS.One.Microsoft.com | Stati Uniti centrali | https: \/ /TM-eastus201.AFS.Azure.NET<br>https: \/ /TM-Kailani-ESS.One.Microsoft.com |
| Pubblico | Giappone orientale | https: \/ /japaneast01.AFS.Azure.NET | Giappone occidentale | https: \/ /TM-japaneast01.AFS.Azure.NET |
| Pubblico | Giappone occidentale | https: \/ /japanwest01.AFS.Azure.NET | Giappone orientale | https: \/ /TM-japanwest01.AFS.Azure.NET |
| Pubblico | Corea centrale | https: \/ /koreacentral01.AFS.Azure.NET/ | Corea meridionale | https: \/ /TM-koreacentral01.AFS.Azure.NET/ |
| Pubblico | Corea meridionale | https: \/ /koreasouth01.AFS.Azure.NET/ | Corea centrale | https: \/ /TM-koreasouth01.AFS.Azure.NET/ |
| Pubblico | Stati Uniti centro-settentrionali | https: \/ /northcentralus01.AFS.Azure.NET | Stati Uniti centro-meridionali | https: \/ /TM-northcentralus01.AFS.Azure.NET |
| Pubblico | Europa settentrionale | https: \/ /northeurope01.AFS.Azure.NET<br>https: \/ /kailani7.One.Microsoft.com | Europa occidentale | https: \/ /TM-northeurope01.AFS.Azure.NET<br>https: \/ /TM-kailani7.One.Microsoft.com |
| Pubblico | Stati Uniti centro-meridionali | https: \/ /southcentralus01.AFS.Azure.NET | Stati Uniti centro-settentrionali | https: \/ /TM-southcentralus01.AFS.Azure.NET |
| Pubblico | India meridionale | https: \/ /southindia01.AFS.Azure.NET<br>https: \/ /Kailani-sin.One.Microsoft.com | Central India | https: \/ /TM-southindia01.AFS.Azure.NET<br>https: \/ /TM-Kailani-sin.One.Microsoft.com |
| Pubblico | Asia sud-orientale | https: \/ /southeastasia01.AFS.Azure.NET<br>https: \/ /kailani10.One.Microsoft.com | Asia orientale | https: \/ /TM-southeastasia01.AFS.Azure.NET<br>https: \/ /TM-kailani10.One.Microsoft.com |
| Pubblico | Regno Unito meridionale | https: \/ /uksouth01.AFS.Azure.NET<br>https: \/ /Kailani-UKS.One.Microsoft.com | Regno Unito occidentale | https: \/ /TM-uksouth01.AFS.Azure.NET<br>https: \/ /TM-Kailani-UKS.One.Microsoft.com |
| Pubblico | Regno Unito occidentale | https: \/ /ukwest01.AFS.Azure.NET<br>https: \/ /Kailani-UKW.One.Microsoft.com | Regno Unito meridionale | https: \/ /TM-ukwest01.AFS.Azure.NET<br>https: \/ /TM-Kailani-UKW.One.Microsoft.com |
| Pubblico | Stati Uniti centro-occidentali | https: \/ /westcentralus01.AFS.Azure.NET | West US 2 | https: \/ /TM-westcentralus01.AFS.Azure.NET |
| Pubblico | Europa occidentale | https: \/ /westeurope01.AFS.Azure.NET<br>https: \/ /kailani6.One.Microsoft.com | Europa settentrionale | https: \/ /TM-westeurope01.AFS.Azure.NET<br>https: \/ /TM-kailani6.One.Microsoft.com |
| Pubblico | Stati Uniti Occidentali | https: \/ /westus01.AFS.Azure.NET<br>https: \/ /Kailani.One.Microsoft.com | Stati Uniti orientali | https: \/ /TM-westus01.AFS.Azure.NET<br>https: \/ /TM-Kailani.One.Microsoft.com |
| Pubblico | West US 2 | https: \/ /westus201.AFS.Azure.NET | Stati Uniti centro-occidentali | https: \/ /TM-westus201.AFS.Azure.NET |
| Enti governativi | US Gov Arizona | https: \/ /usgovarizona01.AFS.Azure.US | US Gov Texas | https: \/ /TM-usgovarizona01.AFS.Azure.US |
| Enti governativi | US Gov Texas | https: \/ /usgovtexas01.AFS.Azure.US | US Gov Arizona | https: \/ /TM-usgovtexas01.AFS.Azure.US |

- Se si usano account di archiviazione con ridondanza locale (LRS) o con ridondanza della zona (ZRS), è sufficiente abilitare l'URL elencato in "URL dell'endpoint primario".

- Se si usano account di archiviazione con ridondanza geografica (GRS), abilitare tre URL.

**Esempio:** si distribuisce un servizio di sincronizzazione archiviazione in `"West US"` e si registra il server con esso. Gli URL con cui consentire al server di comunicare per questo caso sono:

> - https: \/ /westus01.AFS.Azure.NET (endpoint primario: Stati Uniti occidentali)
> - https: \/ /eastus01.AFS.Azure.NET (area di failover abbinata: Stati Uniti orientali)
> - https: \/ /TM-westus01.AFS.Azure.NET (URL di individuazione dell'area primaria)

### <a name="allow-list-for-azure-file-sync-ip-addresses"></a>Elenco Consenti per Sincronizzazione file di Azure indirizzi IP
Sincronizzazione file di Azure supporta l'uso di [tag di servizio](../../virtual-network/service-tags-overview.md), che rappresentano un gruppo di prefissi di indirizzi IP per un determinato servizio di Azure. È possibile usare i tag di servizio per creare regole del firewall che consentono la comunicazione con il servizio Sincronizzazione file di Azure. Il tag di servizio per Sincronizzazione file di Azure è `StorageSyncService` .

Se si usa Sincronizzazione file di Azure in Azure, è possibile usare il nome del tag di servizio direttamente nel gruppo di sicurezza di rete per consentire il traffico. Per altre informazioni, vedere [Gruppi di sicurezza di rete](../../virtual-network/security-overview.md).

Se si usa Sincronizzazione file di Azure in locale, è possibile usare l'API dei tag di servizio per ottenere gli specifici intervalli di indirizzi IP per l'elenco di elementi consentiti del firewall. Per recuperare queste informazioni, sono disponibili due metodi:

- L'elenco corrente degli intervalli di indirizzi IP per tutti i servizi di Azure che supportano i tag di servizio viene pubblicato settimanalmente nell'Area download Microsoft sotto forma di documento JSON. Ogni cloud di Azure è associato a uno specifico documento JSON con gli intervalli di indirizzi IP pertinenti:
    - [Pubblico di Azure](https://www.microsoft.com/download/details.aspx?id=56519)
    - [Azure US Gov](https://www.microsoft.com/download/details.aspx?id=57063)
    - [Azure per la Cina](https://www.microsoft.com/download/details.aspx?id=57062)
    - [Azure Germania](https://www.microsoft.com/download/details.aspx?id=57064)
- L'API di individuazione di tag di servizio (anteprima) consente il recupero a livello di codice dell'elenco corrente di tag di servizio. Nella versione di anteprima questa API potrebbe restituire informazioni meno aggiornate di quelle restituite dai documenti JSON pubblicati nell'Area download Microsoft. È possibile usare la superficie dell'API in base alle preferenze di automazione:
    - [REST API](https://docs.microsoft.com/rest/api/virtualnetwork/servicetags/list)
    - [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.network/Get-AzNetworkServiceTag)
    - [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/network#az-network-list-service-tags)

Poiché l'API di individuazione tag di servizio non viene aggiornata con la stessa frequenza con cui vengono pubblicati i documenti JSON nell'area download Microsoft, è consigliabile usare il documento JSON per aggiornare l'elenco Consenti del firewall locale. Attenersi alla procedura riportata di seguito:

```PowerShell
# The specific region to get the IP address ranges for. Replace westus2 with the desired region code 
# from Get-AzLocation.
$region = "westus2"

# The service tag for Azure File Sync. Do not change unless you're adapting this
# script for another service.
$serviceTag = "StorageSyncService"

# Download date is the string matching the JSON document on the Download Center. 
$possibleDownloadDates = 0..7 | `
    ForEach-Object { [System.DateTime]::Now.AddDays($_ * -1).ToString("yyyyMMdd") }

# Verify the provided region
$validRegions = Get-AzLocation | `
    Where-Object { $_.Providers -contains "Microsoft.StorageSync" } | `
    Select-Object -ExpandProperty Location

if ($validRegions -notcontains $region) {
    Write-Error `
            -Message "The specified region $region is not available. Either Azure File Sync is not deployed there or the region does not exist." `
            -ErrorAction Stop
}

# Get the Azure cloud. This should automatically based on the context of 
# your Az PowerShell login, however if you manually need to populate, you can find
# the correct values using Get-AzEnvironment.
$azureCloud = Get-AzContext | `
    Select-Object -ExpandProperty Environment | `
    Select-Object -ExpandProperty Name

# Build the download URI
$downloadUris = @()
switch($azureCloud) {
    "AzureCloud" { 
        $downloadUris = $possibleDownloadDates | ForEach-Object {  
            "https://download.microsoft.com/download/7/1/D/71D86715-5596-4529-9B13-DA13A5DE5B63/ServiceTags_Public_$_.json"
        }
    }

    "AzureUSGovernment" {
        $downloadUris = $possibleDownloadDates | ForEach-Object { 
            "https://download.microsoft.com/download/6/4/D/64DB03BF-895B-4173-A8B1-BA4AD5D4DF22/ServiceTags_AzureGovernment_$_.json"
        }
    }

    "AzureChinaCloud" {
        $downloadUris = $possibleDownloadDates | ForEach-Object { 
            "https://download.microsoft.com/download/9/D/0/9D03B7E2-4B80-4BF3-9B91-DA8C7D3EE9F9/ServiceTags_China_$_.json"
        }
    }

    "AzureGermanCloud" {
        $downloadUris = $possibleDownloadDates | ForEach-Object { 
            "https://download.microsoft.com/download/0/7/6/076274AB-4B0B-4246-A422-4BAF1E03F974/ServiceTags_AzureGermany_$_.json"
        }
    }

    default {
        Write-Error -Message "Unrecognized Azure Cloud: $_" -ErrorAction Stop
    }
}

# Find most recent file
$found = $false 
foreach($downloadUri in $downloadUris) {
    try { $response = Invoke-WebRequest -Uri $downloadUri -UseBasicParsing } catch { }
    if ($response.StatusCode -eq 200) {
        $found = $true
        break
    }
}

if ($found) {
    # Get the raw JSON 
    $content = [System.Text.Encoding]::UTF8.GetString($response.Content)

    # Parse the JSON
    $serviceTags = ConvertFrom-Json -InputObject $content -Depth 100

    # Get the specific $ipAddressRanges
    $ipAddressRanges = $serviceTags | `
        Select-Object -ExpandProperty values | `
        Where-Object { $_.id -eq "$serviceTag.$region" } | `
        Select-Object -ExpandProperty properties | `
        Select-Object -ExpandProperty addressPrefixes
} else {
    # If the file cannot be found, that means there hasn't been an update in
    # more than a week. Please verify the download URIs are still accurate
    # by checking https://docs.microsoft.com/azure/virtual-network/service-tags-overview
    Write-Verbose -Message "JSON service tag file not found."
    return
}
```

È quindi possibile usare gli intervalli di indirizzi IP in `$ipAddressRanges` per aggiornare il firewall. Per informazioni su come aggiornare il firewall, vedere il sito Web del firewall o dell'appliance di rete.

## <a name="test-network-connectivity-to-service-endpoints"></a>Testare la connettività di rete agli endpoint di servizio
Una volta che un server è stato registrato con il servizio Sincronizzazione file di Azure, è possibile utilizzare il cmdlet Test-StorageSyncNetworkConnectivity e ServerRegistration.exe per verificare le comunicazioni con tutti gli endpoint (URL) specifici del server. Questo cmdlet consente di risolvere i problemi quando la comunicazione incompleta impedisce al server di lavorare completamente con Sincronizzazione file di Azure e può essere usata per ottimizzare le configurazioni del proxy e del firewall.

Per eseguire il test della connettività di rete, installare Sincronizzazione file di Azure Agent 9,1 o versione successiva ed eseguire i comandi di PowerShell seguenti:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Test-StorageSyncNetworkConnectivity
```

## <a name="summary-and-risk-limitation"></a>Riepilogo e limitazione dei rischi
Gli elenchi riportati in questo documento contengono gli URL con cui comunica attualmente Sincronizzazione file di Azure. I firewall devono essere in grado di consentire il traffico in uscita da questi domini. Microsoft si impegna a mantenere l'elenco costantemente aggiornato.

La configurazione di regole del firewall con limitazione del dominio può contribuire a migliorare la sicurezza. Se vengono usate queste configurazioni del firewall, è necessario tenere presente che nel tempo verranno aggiunti nuovi URL e potrebbero essere modificati quelli esistenti. Controllare periodicamente questo articolo.

## <a name="next-steps"></a>Passaggi successivi
- [Pianificazione per la distribuzione di Sincronizzazione file di Azure](storage-sync-files-planning.md)
- [Come distribuire Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md)
- [Monitorare Sincronizzazione file di Azure](storage-sync-files-monitoring.md)
