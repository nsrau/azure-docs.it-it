---
title: Impostazioni di proxy e firewall locali di Sincronizzazione file di Azure | Microsoft Docs
description: Configurazione della rete locale di Sincronizzazione file di Azure
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 11/26/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: d9b7296a116ebd06542a53087afbd083dbd3a7eb
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64696756"
---
# <a name="azure-file-sync-proxy-and-firewall-settings"></a>Impostazioni di proxy e firewall di Sincronizzazione file di Azure
Sincronizzazione file di Azure connette i server locali a File di Azure abilitando la sincronizzazione tra più siti e funzionalità di suddivisione in livelli cloud. È necessario quindi che un server locale sia connesso a Internet e che un amministratore IT scelga il percorso migliore per consentire al server di accedere ai servizi cloud di Azure.

Questo articolo offre informazioni dettagliate sui requisiti e le opzioni disponibili per connettere un server a Sincronizzazione file di Azure in modo sicuro ed efficiente.

> [!Important]
> Sincronizzazione file di Azure non supporta ancora firewall e reti virtuali per gli account di archiviazione.

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

**Le impostazioni proxy a livello di computer** consentono di configurare il proxy in modo specifico per il traffico di Sincronizzazione file di Azure. Le impostazioni proxy specifiche per le app sono supportate nella versione 4.0.1.0 o versioni successive dell'agente e possono essere configurate durante l'installazione dell'agente oppure usando il cmdlet Set-StorageSyncProxyConfiguration di PowerShell.

Comandi di PowerShell per configurare le impostazioni proxy specifiche dell'app:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Set-StorageSyncProxyConfiguration -Address <url> -Port <port number> -ProxyCredential <credentials>
```
**Le impostazioni proxy a livello di computer** sono trasparenti per l'agente Sincronizzazione file di Azure poiché tutto il traffico diretto al server viene instradato tramite il proxy.

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

      Note: il servizio Storage Sync Agent (filesyncsvc) si avvia in modo automatico dopo l'arresto.

## <a name="firewall"></a>Firewall
Come indicato in una sezione precedente, la porta 443 deve essere aperta in uscita. In base ai criteri definiti a livello di data center, ramo o area geografica, è possibile che sia necessario o preferibile limitare il traffico su questa porta a domini specifici.

La tabella seguente illustra i domini necessari per la comunicazione:

| Service | Endpoint del cloud pubblico | Endpoint di Azure per enti pubblici | Uso |
|---------|----------------|---------------|------------------------------|
| **Azure Resource Manager** | https://management.azure.com | https://management.usgovcloudapi.net | Qualsiasi chiamata utente (ad esempio, PowerShell) giunge o passa attraverso questo URL, inclusa la chiamata di registrazione iniziale del server. |
| **Azure Active Directory** | https://login.windows.net | https://login.microsoftonline.us | Le chiamate di Azure Resource Manager devono essere effettuate da un utente autenticato. Per l'autenticazione utente viene usato questo URL. |
| **Azure Active Directory** | https://graph.windows.net/ | https://graph.windows.net/ | Nel corso del processo di distribuzione di Sincronizzazione file di Azure verrà creata un'entità servizio nella sessione di Azure Active Directory associata alla sottoscrizione. A tale scopo viene usato questo URL. Questa entità viene usata per delegare una quantità minima di diritti al servizio Sincronizzazione file di Azure. L'utente che esegue la configurazione iniziale di Sincronizzazione file di Azure deve essere un utente autenticato con privilegi di proprietario della sottoscrizione. |
| **Archiviazione di Azure** | &ast;.core.windows.net | &ast;.core.usgovcloudapi.net | Quando il server scarica un file, esegue lo spostamento dati in modo più efficiente se comunica direttamente con la condivisione file di Azure nell'account di archiviazione. Il server ha una chiave di firma di accesso condiviso che consente l'accesso solo a specifiche condivisioni file. |
| **Sincronizzazione file di Azure** | &ast;.one.microsoft.com | &ast;.afs.azure.us | Dopo la registrazione iniziale, il server riceve un URL regionale relativo all'istanza del servizio Sincronizzazione file di Azure disponibile in quell'area. Il server può usare l'URL per comunicare direttamente e in modo efficiente con l'istanza che gestisce la sincronizzazione. |
| **Infrastruttura a chiave pubblica Microsoft** | `https://www.microsoft.com/pki/mscorp`<br /><http://ocsp.msocsp.com> | `https://www.microsoft.com/pki/mscorp`<br /><http://ocsp.msocsp.com> | Dopo l'installazione dell'agente di Sincronizzazione file di Azure, l'URL dell'infrastruttura a chiave pubblica viene usato per scaricare i certificati intermedi necessari per comunicare con il servizio Sincronizzazione file di Azure e la condivisione file di Azure. L'URL OCSP viene usato per controllare lo stato di un certificato. |

> [!Important]
> Quando si consente il traffico a &ast;.one.microsoft.com, il traffico dal server non è più limitato solo al servizio di sincronizzazione. Nei sottodomini sono infatti disponibili molti altri servizi Microsoft.

If &ast;.one.microsoft.com è troppo ampio, è possibile limitare le comunicazioni del server consentendo le comunicazioni solo con esplicite istanze regionali del servizio Sincronizzazione file di Azure. Le istanze da scegliere dipendono dall'area del servizio di sincronizzazione archiviazione in cui è stato distribuito e registrato il server. Tale area è chiamata "URL dell'endpoint primario" nella tabella seguente.

Per ragioni di continuità aziendale e ripristino di emergenza (BCDR) è possibile che siano state specificate condivisioni file di Azure in un account di archiviazione con ridondanza geografica (GRS). In tal caso, le condivisioni file di Azure eseguiranno il failover nell'area abbinata se si verifica un'interruzione duratura a livello di area. Sincronizzazione file di Azure usa le stesse associazioni a livello di area della risorsa di archiviazione. Pertanto, se si usano account di archiviazione con ridondanza geografica, è necessario abilitare URL aggiuntivi per consentire al server di comunicare con l'area abbinata per Sincronizzazione file di Azure. Nella tabella seguente questa è chiamata "area abbinata". Inoltre, vi è anche un URL del profilo di gestione traffico che deve essere abilitato. In questo modo il traffico di rete può essere indirizzato di nuovo con facilità all'area abbinata in caso di failover ed è chiamato "URL di individuazione" nella tabella seguente.

| Cloud  | Region | URL dell'endpoint primario | Area associata | URL di individuazione |
|--------|--------|----------------------|---------------|---------------|
| Pubblico |Australia orientale | https://kailani-aue.one.microsoft.com | Australia sud-orientale | https://kailani-aue.one.microsoft.com |
| Pubblico |Australia sud-orientale | https://kailani-aus.one.microsoft.com | Australia orientale | https://tm-kailani-aus.one.microsoft.com |
| Pubblico | Canada centrale | https://kailani-cac.one.microsoft.com | Canada orientale | https://tm-kailani-cac.one.microsoft.com |
| Pubblico | Canada orientale | https://kailani-cae.one.microsoft.com | Canada centrale | https://tm-kailani.cae.one.microsoft.com |
| Pubblico | Stati Uniti centrali | https://kailani-cus.one.microsoft.com | Stati Uniti orientali 2 | https://tm-kailani-cus.one.microsoft.com |
| Pubblico | Asia orientale | https://kailani11.one.microsoft.com | Asia sud-orientale | https://tm-kailani11.one.microsoft.com |
| Pubblico | Stati Uniti orientali | https://kailani1.one.microsoft.com | Stati Uniti occidentali | https://tm-kailani1.one.microsoft.com |
| Pubblico | Stati Uniti orientali 2 | https://kailani-ess.one.microsoft.com | Stati Uniti centrali | https://tm-kailani-ess.one.microsoft.com |
| Pubblico | Europa settentrionale | https://kailani7.one.microsoft.com | Europa occidentale | https://tm-kailani7.one.microsoft.com |
| Pubblico | Asia sud-orientale | https://kailani10.one.microsoft.com | Asia orientale | https://tm-kailani10.one.microsoft.com |
| Pubblico | Regno Unito meridionale | https://kailani-uks.one.microsoft.com | Regno Unito occidentale | https://tm-kailani-uks.one.microsoft.com |
| Pubblico | Regno Unito occidentale | https://kailani-ukw.one.microsoft.com | Regno Unito meridionale | https://tm-kailani-ukw.one.microsoft.com |
| Pubblico | Europa occidentale | https://kailani6.one.microsoft.com | Europa settentrionale | https://tm-kailani6.one.microsoft.com |
| Pubblico | Stati Uniti occidentali | https://kailani.one.microsoft.com | Stati Uniti orientali | https://tm-kailani.one.microsoft.com |
| Government | US Gov Arizona | https://usgovarizona01.afs.azure.us | US Gov Texas | https://tm-usgovarizona01.afs.azure.us |
| Government | US Gov Texas | https://usgovtexas01.afs.azure.us | US Gov Arizona | https://tm-usgovtexas01.afs.azure.us |

- Se si usano account di archiviazione con ridondanza locale (LRS) o con ridondanza della zona (ZRS), è sufficiente abilitare l'URL elencato in "URL dell'endpoint primario".

- Se si usano account di archiviazione con ridondanza geografica (GRS), abilitare tre URL.

**Esempio:** si distribuisce un servizio di sincronizzazione archiviazione in `"West US"` e si registra il server con esso. Gli URL con cui consentire al server di comunicare per questo caso sono:

> - https://kailani.one.microsoft.com (endpoint primario: Stati Uniti occidentali)
> - https://kailani1.one.microsoft.com (area di failover abbinata: Stati Uniti orientali)
> - https://tm-kailani.one.microsoft.com (URL di individuazione dell'area primaria)

## <a name="summary-and-risk-limitation"></a>Riepilogo e limitazione dei rischi
Gli elenchi riportati in questo documento contengono gli URL con cui comunica attualmente Sincronizzazione file di Azure. I firewall devono essere in grado di consentire il traffico in uscita da questi domini. Microsoft si impegna a mantenere l'elenco costantemente aggiornato.

La configurazione di regole del firewall con limitazione del dominio può contribuire a migliorare la sicurezza. Se vengono usate queste configurazioni del firewall, è necessario tenere presente che nel tempo verranno aggiunti nuovi URL e potrebbero essere modificati quelli esistenti. Controllare periodicamente questo articolo.

## <a name="next-steps"></a>Passaggi successivi
- [Pianificazione per la distribuzione di Sincronizzazione file di Azure](storage-sync-files-planning.md)
- [Come distribuire Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md)
- [Monitorare Sincronizzazione file di Azure](storage-sync-files-monitoring.md)
