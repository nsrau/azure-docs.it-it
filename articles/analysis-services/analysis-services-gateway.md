---
title: Gateway dati locale | Microsoft Docs
description: "Un gateway locale è necessario se il server Analysis Services di Azure si connette a origini dati locali."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: cd596155-b608-4a34-935e-e45c95d884a9
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 08/21/2017
ms.author: owend
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: 8c142c5e89c5e4eb45bddd0943a6a130cc876f5a
ms.contentlocale: it-it
ms.lasthandoff: 08/24/2017

---
# <a name="connecting-to-on-premises-data-sources-with-azure-on-premises-data-gateway"></a>Connessione a origini dati locali con Gateway dati locale di Azure
Il gateway dati locale svolge la funzione di ponte, garantendo il trasferimento sicuro dei dati tra le origini dati locali e i server Azure Analysis Services nel cloud. Oltre a lavorare con più server Azure Analysis Services nella stessa area, la versione più recente del gateway funziona anche con Microsoft Flow, Power BI, app Power e App per la logica di Azure. È possibile associare più servizi nella stessa area con un singolo gateway. 

 Azure Analysis Services richiede una risorsa gateway nella stessa area. Ad esempio, se si dispone di server di Analysis Services di Azure nell'area Stati Uniti orientali 2, è necessario una risorsa gateway nell'area Stati Uniti orientali 2. Più server in Stati Uniti orientali 2 possono utilizzare lo stesso gateway.

Ottenere il programma di installazione con il gateway la prima volta consiste in un processo in quattro parti:

- **Scaricare ed eseguire il programma di installazione**: questo passaggio consente di installare un servizio gateway su un computer all'interno dell'organizzazione.

- **Registrare il gateway**: in questo passaggio, si specifica un nome e la chiave di ripristino per il gateway e si seleziona un'area, si registra il gateway con il servizio Cloud Gateway.

- **Creare una risorsa per il gateway in Azure**: in questo passaggio, si crea una risorsa per il gateway nella sottoscrizione di Azure.

- **Connettere i server per la risorsa per il gateway**: dopo aver creato una risorsa per il gateway nella sottoscrizione, è possibile iniziare a connettere i server.

Dopo aver configurato una risorsa per il gateway per la sottoscrizione, è possibile connettere più server e altri servizi ad esso. È sufficiente installare un gateway diverso e creare risorse aggiuntive per il gateway se si dispone di server o altri servizi in un'area diversa.

Per iniziare subito, vedere [Installare e configurare il gateway dati locale](analysis-services-gateway-install.md).

## <a name="how-it-works"> </a>Funzionamento
Il gateway installato su un computer dell'organizzazione viene eseguito come servizio Windows, **Gateway dati locale**. Il servizio locale è registrato con il servizio Cloud Gateway tramite il bus di servizio di Azure. È quindi possibile creare una servizio Cloud della risorsa per il gateway per la sottoscrizione di Azure. I server di Azure Analysis Services quindi sono connessi alla risorsa per il gateway. Quando i modelli nel server devono connettersi ai dati locali di origine per le query o l'elaborazione, un flusso di dati e query attraversa la risorsa del gateway, il bus di servizio di Azure, il servizio gateway dati locale e le origini dati. 

![Funzionamento](./media/analysis-services-gateway/aas-gateway-how-it-works.png)

Query e flusso di dati:

1. Il servizio cloud crea una query con le credenziali crittografate per l'origine dati locale. La query viene inviata a una coda per l'elaborazione da parte del gateway.
2. Il servizio cloud del gateway analizza la query e inserisce la richiesta nel [bus di servizio di Azure](https://azure.microsoft.com/documentation/services/service-bus/).
3. Il gateway dati locale esegue il polling del bus di servizio per le richieste in sospeso.
4. Il gateway riceve la query, decrittografa le credenziali e si connette alle origini dati usando tali credenziali.
5. Invia quindi la query all'origine dati per l'esecuzione.
6. I risultati vengono inviati dall'origine dati al gateway e quindi al servizio cloud e al server.

## <a name="windows-service-account"> </a>Account del servizio Windows
Il gateway dati locale è configurato per usare *NT SERVICE\PBIEgwService* come credenziale di accesso al servizio Windows. Per impostazione predefinita, dispone del diritto di accesso come servizio nel contesto del computer in cui viene installato il gateway. Questa credenziale non coincide con l'account usato per la connessione alle origini dati locali o con l'account Azure.  

Se si verificano problemi di autenticazione con il server proxy, può essere necessario trasformare l'account del servizio Windows nell'account di un utente del dominio o nell'account di un servizio gestito.

## <a name="ports"> </a>Porte
Il gateway crea una connessione in uscita al bus di servizio di Azure. Comunica sulle porte in uscita seguenti: TCP 443 (impostazione predefinita), 5671, 5672 e da 9350 a 9354.  Non sono richieste porte in ingresso.

È consigliabile inserire nell'elenco degli elementi consentiti gli indirizzi IP per l'area dati del firewall. È possibile scaricare l'[elenco degli indirizzi IP dei data center di Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). L'elenco viene aggiornato ogni settimana.

> [!NOTE]
> Gli indirizzi IP inclusi nell'elenco degli indirizzi IP dei data center di Azure sono nella notazione CIDR. Ad esempio, 10.0.0.0/24 non significa da 10.0.0.0 a 10.0.0.24. Per altre informazioni, vedere [CIDR notation](http://whatismyipaddress.com/cidr) (Notazione CIDR).
>
>

Di seguito sono indicati i nomi di dominio completi usati dal gateway.

| Nomi di dominio | Porte in uscita | Descrizione |
| --- | --- | --- |
| *.powerbi.com |80 |HTTP usato per scaricare il programma di installazione. |
| *.powerbi.com |443 |HTTPS |
| *. analysis.windows.net |443 |HTTPS |
| *.login.windows.net |443 |HTTPS |
| *.servicebus.windows.net |5671-5672 |Advanced Message Queuing Protocol (AMQP) |
| *.servicebus.windows.net |443, 9350-9354 |Listener in Inoltro del Bus di servizio su TCP (richiede 443 per l'acquisizione del token di Controllo di accesso) |
| *.frontend.clouddatahub.net |443 |HTTPS |
| *.core.windows.net |443 |HTTPS |
| login.microsoftonline.com |443 |HTTPS |
| *.msftncsi.com |443 |Usato per testare la connettività a Internet se il gateway non è raggiungibile dal servizio Power BI. |
| *.microsoftonline-p.com |443 |Usato per l'autenticazione, a seconda della configurazione. |

### <a name="force-https"></a>Forzare la comunicazione HTTPS con il bus di servizio di Azure
È possibile forzare il gateway a comunicare con il bus di servizio di Azure usando HTTPS anziché TCP diretto. Questa operazione, tuttavia, può ridurre le prestazioni in misura significativa. È necessario modificare il file *Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config* modificando il valore da `AutoDetect` in `Https`. Questo file si trova solitamente in *C:\Programmi\On-premises data gateway*.

```
<setting name="ServiceBusSystemConnectivityModeString" serializeAs="String">
    <value>Https</value>
</setting>
```

## <a name="faq"></a>Domande frequenti

### <a name="general"></a>Generale

**D**: È necessario un gateway per le origini dati nel cloud, ad esempio SQL Azure? <br/>
**R**: No. Il gateway si connette solo alle origini dati locali.

**D**: Il gateway deve essere installato nello stesso computer dell'origine dati? <br/>
**R**: No. Il gateway si connette all'origine dati tramite le informazioni di connessione fornite. In questo senso il gateway può essere paragonato a un'applicazione client. Il gateway deve solo potersi connettere al nome del server specificato.

<a name="why-azure-work-school-account"></a>

**D**: Perché è necessario utilizzare un account aziendale o dell'istituto di istruzione per accedere? <br/>
**R**: È possibile usare solo un account di Azure aziendale o dell'istituto di istruzione quando si installa il gateway dati locale. L'account di accesso viene archiviato in un tenant gestito da Azure Active Directory (Azure AD). Il nome dell'entità utente (UPN) dell'account di Azure AD in genere corrisponde all'indirizzo di posta elettronica.

**D**: Dove sono archiviate le credenziali? <br/>
**R**: Le credenziali immesse per un'origine dati vengono crittografate e archiviate nel servizio cloud del gateway. Le credenziali vengono quindi decrittografate nel gateway dati locale.

**D**: Sono previsti requisiti per la larghezza di banda della rete? <br/>
**R**: È consigliabile che la connessione di rete abbia una buona velocità effettiva. Ogni ambiente è diverso e la quantità di dati inviati influisce sui risultati. L'uso di ExpressRoute può contribuire a garantire un livello di velocità effettiva tra i data center di Azure e quelli locali.
Lo strumenti di terze parti Azure Speed Test può aiutare a valutare la velocità effettiva.

**D**: Qual è la latenza per l'esecuzione di query a un'origine dati dal gateway? Qual è l'architettura ottimale? <br/>
**R**: Per ridurre la latenza di rete è consigliabile installare il gateway il più vicino possibile all'origine dati. Installando il gateway nell'origine dati effettiva, la latenza introdotta risulterà ridotta al minimo grazie a questa prossimità. Si considerino anche i data center. Se, ad esempio, il servizio usa il data center degli Stati Uniti occidentali e SQL Server è ospitato in una macchina virtuale di Azure, è opportuno che anche la macchina virtuale di Azure sia ubicata negli Stati Uniti occidentali. Grazie a questa prossimità la latenza è ridotta al minimo e si evitano addebiti relativi ai dati in uscita sulla macchina virtuale di Azure.

**D**: In che modo i risultati vengono inviati al cloud? <br/>
**R**: I risultati vengono inviati tramite il Bus di servizio di Azure.

**D**: Esistono connessioni in ingresso al gateway dal cloud? <br/>
**R**: No. Il gateway usa le connessioni in uscita al bus di servizio di Azure.

**D**: Cosa accade se si bloccano le connessioni in uscita? Cosa fare per sbloccarle? <br/>
**R**: Visualizzare le porte e gli host usati dal gateway.

**D**: Come viene chiamato il servizio Windows effettivo?<br/>
**R**: Nei servizi il gateway è denominato servizio Power BI Gateway Enterprise.

**D**: Il servizio gateway di Windows può essere eseguito con un account Azure Active Directory? <br/>
**R**: No. Il servizio Windows deve disporre di un account Windows valido. Per impostazione predefinita, il sevizio viene eseguito con il SID servizio NT SERVICE\PBIEgwService.

### <a name="high-availability"></a>Disponibilità elevata e ripristino di emergenza

**D**: Quali opzioni sono disponibili per il ripristino di emergenza? <br/>
**R**: È possibile usare la chiave di ripristino per ripristinare o spostare un gateway. La chiave di ripristino viene specificata al momento dell'installazione del gateway.

**D**: Qual è il vantaggio della chiave di ripristino? <br/>
**R**: La chiave di ripristino consente di eseguire la migrazione o di ripristinare le impostazioni del gateway in caso di emergenza.

## <a name="troubleshooting"> </a>Risoluzione dei problemi

**D**: Come è possibile visualizzare le query inviate all'origine dati locale? <br/>
**R**: È possibile abilitare la funzione di tracciamento delle query, che include le query inviate. Dopo aver risolto il problema, ripristinare il valore originale per il tracciamento delle query. Se il tracciamento delle query non viene disabilitato, si creeranno dei log più grandi.

È anche possibile usare gli strumenti per il tracciamento delle query di cui è dotata l'origine dati. Ad esempio, è possibile usare Eventi estesi o SQL Profiler per SQL Server e Analysis Services.

**D**: Dove si trovano i log del gateway? <br/>
**R**: Vedere la sezione Strumenti più avanti in questo argomento.

### <a name="update"></a>Aggiornare alla versione più recente

Quando la versione del gateway non è aggiornata, possono emergere numerosi problemi. Come buona pratica, assicurarsi di usare la versione più recente. Se il gateway non è stato aggiornato per un mese o più è consigliabile installarne la versione più recente e verificare se è possibile riprodurre il problema.

### <a name="error-failed-to-add-user-to-group--2147463168-pbiegwservice-performance-log-users"></a>Errore: Impossibile aggiungere l'utente al gruppo. (Utenti log delle prestazioni -2147463168 PBIEgwService)

Questo errore viene visualizzato se si sta tentando di installare il gateway in un controller di dominio, un'operazione non consentita. Assicurarsi di distribuire il gateway in un computer che non sia un controller di dominio.

## <a name="logs"></a>Log

I file di log sono di fondamentale importanza per la risoluzione dei problemi.

#### <a name="enterprise-gateway-service-logs"></a>Log del servizio gateway Enterprise

`C:\Users\PBIEgwService\AppData\Local\Microsoft\On-premises data gateway\<yyyyymmdd>.<Number>.log`

#### <a name="configuration-logs"></a>Log di configurazione

`C:\Users\<username>\AppData\Local\Microsoft\On-premises data gateway\GatewayConfigurator.log`




#### <a name="event-logs"></a>Log eventi

I log di Gateway di gestione dati e PowerBIGateway sono reperibili in **Registri applicazioni e servizi**.


## <a name="telemetry"></a>Telemetria
La telemetria può essere usata per il monitoraggio e la risoluzione dei problemi. Per impostazione predefinita

**Per attivare la telemetria**

1.  Verificare la directory del client gateway dati locale nel computer. In genere è **%unitàsistema%\Programmi\Gateway dati locale**. Oppure è possibile aprire una console dei servizi e verificare il percorso al file eseguibile, una proprietà del servizio gateway dati locale.
2.  Nel file Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config nella directory del client modificare l'impostazione SendTelemetry su true.
        
    ```
        <setting name="SendTelemetry" serializeAs="String">
                    <value>true</value>
        </setting>
    ```

3.  Salvare le modifiche e riavviare il servizio Windows gateway dati locale.




## <a name="next-steps"></a>Passaggi successivi
* [Gestire Analysis Services](analysis-services-manage.md)
* [Ottenere dati da Azure Analysis Services](analysis-services-connect.md)

