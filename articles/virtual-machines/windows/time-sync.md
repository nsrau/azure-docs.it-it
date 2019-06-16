---
title: Sincronizzazione dell'ora per macchine virtuali Windows in Azure | Microsoft Docs
description: Sincronizzazione dell'ora per macchine virtuali Windows.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: cynthn
ms.openlocfilehash: aac0a3ab14cc2543fe3b60f4c52e14e3cb0ee743
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65991704"
---
# <a name="time-sync-for-windows-vms-in-azure"></a>Sincronizzazione dell'ora per macchine virtuali Windows in Azure

La sincronizzazione dell'ora è importante per la sicurezza e la correlazione degli eventi. In alcuni casi viene usata per l'implementazione di transazioni distribuite. La precisione dell'ora tra più sistemi di computer si ottiene con la sincronizzazione. La sincronizzazione può essere interessata da più fattori, tra cui i riavvii e il traffico di rete tra l'origine dell'ora e il computer che la recupera. 

Azure è ora supportato da un'infrastruttura che esegue Windows Server 2016. Windows Server 2016 ha migliorato gli algoritmi usati per correggere l'ora e imporre all'orologio locale la sincronizzazione con l'ora UTC.  In Windows Server 2016 è anche migliorato il servizio VMICTimeSync che stabilisce come sincronizzare le macchine virtuali con l'host per avere l'ora esatta. I miglioramenti includono un'ora iniziale più accurata per l'avvio o il ripristino della macchina virtuale e la correzione della latenza di interrupt per gli esempi usati dal servizio Ora di Windows (W32time). 


>[!NOTE]
>Per una rapida panoramica del servizio Ora di Windows, guardare questo [video con informazioni generali di alto livello](https://aka.ms/WS2016TimeVideo).
>
> Per altre informazioni, vedere [Accurate time for Windows Server 2016](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time) (Ora esatta per Windows Server 2016). 

## <a name="overview"></a>Panoramica

La precisione di un orologio del computer viene misurata in base a quanto l'orologio del computer si avvicina allo standard dell'ora UTC. L'ora UTC è definita da un campione multinazionale di orologi atomici precisi che possono essere sfalsati solo di un secondo in 300 anni. Tuttavia, leggere l'ora UTC direttamente richiede un hardware specializzato. Al contrario, i server di riferimento ora vengono sincronizzati con UTC e sono accessibili da altri computer in modo da garantire scalabilità e affidabilità. In ogni computer viene eseguito un servizio di sincronizzazione dell'ora che sa quali server di riferimento ora usare e verifica periodicamente se l'orologio del computer deve essere corretto e, se necessario, regola l'ora. 

Gli host di Azure vengono sincronizzati con i server di riferimento ora interni di Microsoft che ricavano l'ora da dispositivi di strato 1 di proprietà di Microsoft, con antenne GPS. Le macchine virtuali di Azure possono dipendere dal proprio host per passare l'ora esatta (*ora host*) alla macchina virtuale oppure la macchina virtuale può ottenere direttamente l'ora da un server di riferimento ora o una combinazione di entrambi. 

Le interazioni delle macchine virtuali con l'host possono influire sull'orologio. Durante la [manutenzione con mantenimento della memoria](maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot) le macchine virtuali vengono messe in pausa fino a 30 secondi. Ad esempio, prima che inizi la manutenzione l'orologio della macchina virtuale indica 10:00:00 AM e dura 28 secondi. Quando l'esecuzione della macchina virtuale riprende, l'orologio della macchina virtuale indicherebbe ancora 10:00:00 AM, con un ritardo di 28 secondi. Per correggere questo inconveniente, il servizio VMICTimeSync monitora ciò che accade nell'host e chiede di apportare modifiche alle macchine virtuali per compensare.

Il servizio VMICTimeSync opera sia in modalità di campionamento che in modalità di sincronizzazione e influenza solo l'orologio in avanti. In modalità di campionamento, che richiede che W32time sia in esecuzione, il servizio VMICTimeSync esegue il polling dell'host ogni 5 secondi e fornisce campioni temporali a W32time. Ogni 30 secondi circa il servizio W32time acquisisce il campione temporale più recente e lo usa per influenzare l'orologio del guest. La modalità di sincronizzazione viene attivata se l'esecuzione di un guest è stata ripresa o se l'orologio di un guest rimane indietro più di 5 secondi rispetto all'orologio dell'host. Se il servizio W32time funziona correttamente, quest'ultimo caso non dovrebbe mai verificarsi.

Senza il lavoro di sincronizzazione dell'ora, l'orologio della macchina virtuale può accumulare errori. Quando è presente solo una macchina virtuale, l'effetto può non essere significativo, a meno che il carico di lavoro richieda una misurazione del tempo estremamente precisa. Tuttavia, nella maggior parte dei casi sono disponibili più macchine virtuali interconnesse che usano l'ora per tenere traccia delle transazioni e l'ora deve essere coerente in tutta la distribuzione. Quando l'ora è diversa tra le macchine virtuali, si possono osservare gli effetti seguenti:

- L'autenticazione ha esito negativo. I protocolli di sicurezza come Kerberos o la tecnologia dipendente dal certificato si basano sulla coerenza dell'ora tra i sistemi. 
- È molto difficile capire cosa è successo in un sistema se le ore non coincidono nei log o in altri dati. Può sembrare che lo stesso evento si sia verificato in momenti diversi e questo rende difficile la correlazione.
- Se orologio è disattivato, la fatturazione può essere calcolata in modo non corretto.

I migliori risultati per le distribuzioni di Windows si ottengono usando Windows Server 2016 come sistema operativo guest, che consente di usare i miglioramenti più recenti in termini di sincronizzazione dell'ora.

## <a name="configuration-options"></a>Opzioni di configurazione

Sono disponibili tre opzioni per la configurazione della sincronizzazione dell'ora per le macchine virtuali Windows ospitate in Azure:

- Tempo dell'host e time.windows.com. Questa è la configurazione predefinita usata nelle immagini di Azure Marketplace.
- Solo per l'host.
- Usare un altro server di riferimento ora esterno, con o senza l'ora dell'host.


### <a name="use-the-default"></a>Usare l'impostazione predefinita

Per impostazione predefinita, le immagini di macchina virtuale del sistema operativo Windows sono configurate per w32time per la sincronizzazione da due origini: 

- Il provider NtpClient, che ottiene le informazioni da time.windows.com.
- Il servizio VMICTimeSync, usato per comunicare l'ora dell'host alle macchine virtuali e apportare correzioni dopo che la macchina virtuale viene messa in pausa per la manutenzione. Gli host di Azure usano i dispositivi strato 1 di proprietà di Microsoft per mantenere l'ora esatta.

w32time preferisce il provider di servizi orari nel seguente ordine di priorità: livello strato, ritardo radice, dispersione radice, differenza di orario. Nella maggior parte dei casi, w32time preferisce time.windows.com all'host perché time.windows.com segnala uno strato inferiore. 

Per i computer appartenenti a un dominio, lo stesso dominio stabilisce una gerarchia di sincronizzazione dell'ora, ma la radice della foresta proveniente da un punto ha comunque bisogno di tempo e le considerazioni seguenti restano vere.


### <a name="host-only"></a>Solo host 

Poiché time.windows.com è un server NTP pubblico, la sincronizzazione dell'ora con esso richiede l'invio di traffico su Internet, modificare i ritardi dei pacchetti può influire negativamente sulla qualità della sincronizzazione dell'ora. Rimuovere time.windows.com passando alla sincronizzazione solo per l'host a volte può migliorare i risultati della sincronizzazione dell'ora.

Il passaggio alla sincronizzazione dell'ora solo per l'host ha senso se si verificano problemi di sincronizzazione dell'ora usando la configurazione predefinita. Provare la sincronizzazione solo per l'host per verificare se contribuisce a migliorare la sincronizzazione dell'ora nella macchina virtuale. 

### <a name="external-time-server"></a>Server di riferimento ora esterno

In caso di requisiti specifici per la sincronizzazione dell'ora, è anche disponibile un'opzione per l'uso di server di riferimento ora esterni. I server di riferimento ora esterni sono in grado di specificare orari precisi, che possono essere utili per gli scenari di test, garantendo l'uniformità dell'ora con i computer ospitati nei data center non Microsoft o gestendo i secondi intercalari in modo speciale.

È possibile combinare i server esterni con il servizio VMICTimeSync e VMICTimeProvider per avere risultati simili alla configurazione predefinita. 

## <a name="check-your-configuration"></a>Verificare la configurazione


Verificare se il provider servizi orari NtpClient è configurato per usare i server NTP espliciti (NTP) o la sincronizzazione dell'ora di dominio (NT5DS).

```
w32tm /dumpreg /subkey:Parameters | findstr /i "type"
```

Se la macchina virtuale usa NTP, verrà visualizzato l'output seguente:

```
Value Name                 Value Type          Value Data
Type                       REG_SZ              NTP
```

Per vedere quali server di riferimento ora usa il provider di servizi orari NtpClient, in un tipo di prompt dei comandi con privilegi elevati:

```
w32tm /dumpreg /subkey:Parameters | findstr /i "ntpserver"
```

Se la macchina virtuale usa l'impostazione predefinita, l'output avrà un aspetto simile al seguente:

```
NtpServer                  REG_SZ              time.windows.com,0x8
```


Per verificare quale provider di servizi orari è attualmente in uso.

```
w32tm /query /source
```


Ecco l'output che è stato visualizzato e cosa significa:
    
- **time.windows.com**: nella configurazione predefinita w32time ottiene l'ora da time.windows.com. La qualità di sincronizzazione dell'ora dipende dalla connettività Internet ed è influenzata dai ritardi dei pacchetti. Questo di solito è l'output dell'impostazione predefinita.
- **Provider di sincronizzazione dell'ora VM IC**: la macchina virtuale sincronizza l'ora dall'host. In genere questo è il risultato se si acconsente esplicitamente a sincronizzare l'ora solo per l'host o se il server NtpServer al momento non è disponibile. 
- *Il server di dominio*: il computer corrente è in un dominio e il dominio definisce la gerarchia di sincronizzazione dell'ora.
- *Un altro server*: w32time è stato configurato in modo esplicito per ottenere l'ora da un altro server. La qualità della sincronizzazione dell'ora dipende dalla qualità di questo server di riferimento ora.
- **Orologio locale CMOS**: l'orologio non è sincronizzato. È possibile ottenere questo output se w32time non ha avuto abbastanza tempo per l'avvio dopo un riavvio o quando non sono disponibili tutte le origini ora configurate.


## <a name="opt-in-for-host-only-time-sync"></a>Consentire in modo esplicito la sincronizzazione dell'ora solo per l'host

Il team di Azure lavora costantemente al miglioramento della sincronizzazione dell'ora negli host ed è in grado di garantire che tutta l'infrastruttura di sincronizzazione dell'ora sia collocata nei data center di proprietà di Microsoft. In caso di problemi di sincronizzazione dell'ora con la configurazione predefinita che preferisce usare time.windows.com come origine ora principale, è possibile usare i comandi seguenti per acconsentire esplicitamente alla sincronizzazione dell'ora solo per l'host.

Contrassegnare il provider VMIC come abilitato. 

```
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\TimeProviders\VMICTimeProvider /v Enabled /t REG_DWORD /d 1 /f
```

Contrassegnare il provider NTPClient come disabilitato.

```
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\TimeProviders\NtpClient /v Enabled /t REG_DWORD /d 0 /f
```

Riavviare il servizio w32time.

```
net stop w32time && net start w32time
```


## <a name="windows-server-2012-and-r2-vms"></a>Windows Server 2012 e macchine virtuali R2 

Windows Server 2012 e Windows Server 2012 R2 hanno impostazioni predefinite diverse per la sincronizzazione dell'ora. Per impostazione predefinita, w32time è configurato in modo da preferire un impatto ridotto del servizio rispetto all'ora precisa. 

Per spostare le distribuzioni di Windows Server 2012 e 2012 R2 per usare le impostazioni predefinite più recenti, che preferiscono l'ora precisa, è possibile applicare le impostazioni seguenti.

Aggiornare il polling di w32time e aggiornare gli intervalli in modo che corrispondano alle impostazioni di Windows Server 2016.

```
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\Config /v MinPollInterval /t REG_DWORD /d 6 /f
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\Config /v MaxPollInterval /t REG_DWORD /d 10 /f
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\Config /v UpdateInterval /t REG_DWORD /d 100 /f
w32tm /config /update
```

Per fare in modo che w32time sia in grado di usare i nuovi intervalli di polling, i server NtpServer devono essere contrassegnati come se venissero usati. Se i server vengono annotati con maschera di flag a 0x1 bit, verrebbe eseguito l'override di questo meccanismo e w32time userebbe SpecialPollInterval. Assicurarsi che i server NTP specificati usino flag a 0x8 bit o nessun flag:

Verificare che i flag vengano usati per i server NTP usati.

```
w32tm /dumpreg /subkey:Parameters | findstr /i "ntpserver"
```

## <a name="next-steps"></a>Passaggi successivi

Di seguito sono riportati i collegamenti a informazioni più dettagliate sulla sincronizzazione dell'ora:

- [Windows Time Service Tools and Settings](https://docs.microsoft.com/windows-server/networking/windows-time-service/Windows-Time-Service-Tools-and-Settings) (Strumenti e impostazioni del servizio Ora di Windows)
- [Windows Server 2016 Improvements ](https://docs.microsoft.com/windows-server/networking/windows-time-service/windows-server-2016-improvements) (Miglioramenti di Windows Server 2016)
- [Accurate Time for Windows Server 2016](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time) (Ora esatta per Windows Server 2016)
- [Support boundary to configure the Windows Time service for high-accuracy environments](https://docs.microsoft.com/windows-server/networking/windows-time-service/support-boundary) (Limiti del supporto per la configurazione del servizio Ora di Windows per gli ambienti ad alta precisione)


