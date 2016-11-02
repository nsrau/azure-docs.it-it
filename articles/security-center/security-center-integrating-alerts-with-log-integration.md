<properties
   pageTitle="Integrazione degli avvisi del Centro sicurezza di Azure con l'integrazione dei log di Azure (Anteprima) | Microsoft Azure"
   description="Questo articolo consente di iniziare a usare gli avvisi del Centro sicurezza con l'integrazione dei log di Azure."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/> 

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/08/2016"
   ms.author="terrylan"/>

# Integrazione degli avvisi del Centro sicurezza PC con l'integrazione dei log di Azure (Anteprima)

Molti team dedicati alle operazioni di sicurezza e alla risposta ai problemi fanno affidamento su una soluzione SIEM (Security Information and Event Management) come punto di partenza per la valutazione e l'analisi degli avvisi di sicurezza. Con l'integrazione dei log di Azure, i clienti possono sincronizzare gli avvisi del Centro sicurezza di Azure, insieme agli eventi di sicurezza delle macchine virtuali raccolti da Diagnostica di Azure e dai log di controllo di Azure, con la propria soluzione SIEM o di analisi dei log quasi in tempo reale.

L'integrazione dei log di Azure è compatibile con HP ArcSight, Splunk, IBM QRadar e altre soluzioni.

## Quali log è possibile integrare?

Azure produce registrazioni complete per ogni servizio. I log sono classificati nel modo seguente:

- **Log di gestione/controllo**, che offrono visibilità sulle operazioni CREATE, UPDATE e DELETE di Azure Resource Manager.
- **Log del piano dati**, che offrono visibilità sugli eventi generati durante l'uso di una risorsa di Azure. Un esempio è rappresentato dal registro eventi di Windows: log eventi dell'applicazione e di sicurezza in una macchina virtuale.

L'integrazione dei log di Azure attualmente supporta l'integrazione di:

- Log delle macchine virtuali di Azure
- Log di controllo di Azure
- Avvisi del Centro sicurezza di Azure

## Installare l'integrazione dei log di Azure

Scaricare l'[integrazione dei log di Azure](https://www.microsoft.com/download/details.aspx?id=53324).

Il servizio di integrazione dei log di Azure raccoglie i dati di telemetria dal computer in cui è installato. I dati di telemetria raccolti sono:

- Eccezioni che si verificano durante l'esecuzione dell'integrazione dei log di Azure
- Metriche relative al numero di query e di eventi elaborati
- Statistiche sulle opzioni della riga di comando Azlog.exe che vengono usate

> [AZURE.NOTE] Per disabilitare la raccolta dei dati di telemetria è possibile deselezionare questa opzione.

## Integrare i log di controllo di Azure e gli avvisi del Centro sicurezza di Azure

1. Aprire il prompt dei comandi e **passare** a **c:\\Program Files\\Microsoft Azure Log Integration**.

2. Eseguire il comando **azlog createazureid** per creare un'[entità servizio di Azure Active Directory](../active-directory/active-directory-application-objects.md) nei tenant di Azure Active Directory (AD) che ospitano le sottoscrizioni di Azure.

    Verrà richiesto l'account di accesso di Azure.

    > [AZURE.NOTE] È necessario essere proprietario o coamministratore della sottoscrizione.

    L'autenticazione in Azure avviene tramite Azure AD. La creazione di un'entità servizio per l'integrazione dei log di Azure crea l'identità di Azure AD a cui viene concesso l'accesso in lettura alle sottoscrizioni di Azure.

3. Eseguire il comando **azlog authorize <ID sottoscrizione>** per assegnare l'accesso in lettura per la sottoscrizione all'entità servizio creata nel passaggio 2. Se non si specifica un **ID sottoscrizione**, all'entità servizio viene assegnato il ruolo lettura per tutte le sottoscrizioni a cui si ha accesso.

    > [AZURE.NOTE] Se si esegue il comando **authorize** immediatamente dopo il comando **createazureid**, possono essere visualizzati avvisi dovuti a una certa latenza tra la creazione dell'account Azure AD e il momento in cui è disponibile per l'uso. Per non visualizzare tali avvisi, attendere circa 10 secondi tra l'esecuzione del comando **createazureid** e l'esecuzione del comando **authorize**.

4. Verificare che nelle cartelle seguenti siano presenti i file JSON del log di controllo:

  - **c:\\Users\\azlog\\AzureResourceManagerJson**
  - **c:\\Users\\azlog\\AzureResourceManagerJsonLD**

5. Verificare che nelle cartelle seguenti siano presenti avvisi del Centro sicurezza di Azure:

  - **c:\\Users\\azlog\\ AzureSecurityCenterJson**
  - **c:\\Users\\azlog\\AzureSecurityCenterJsonLD**

6. Puntare il connettore del server di inoltro del file SIEM standard alla cartella appropriata per reindirizzare i dati all'istanza di SIEM. Per informazioni sulla configurazione SIEM, vedere la pagina relativa alle [configurazioni SIEM](https://azsiempublicdrops.blob.core.windows.net/drops/ALL.htm).

In caso di domande sull'integrazione dei log di Azure, inviare un messaggio di posta elettronica all'indirizzo [AzSIEMteam@microsoft.com](mailto:AzSIEMteam@microsoft.com)

## Passaggi successivi

Per altre informazioni sui log di controllo di Azure e le definizioni delle proprietà, vedere:

- [Operazioni di controllo con Gestione risorse](../resource-group-audit.md)
- [Elencare gli eventi di gestione in una sottoscrizione](https://msdn.microsoft.com/library/azure/dn931934.aspx): per il recupero degli eventi del log di controllo.

Per altre informazioni sul Centro sicurezza, vedere gli argomenti seguenti:

- [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md): informazioni su come gestire e rispondere agli avvisi di sicurezza.
- [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md): domande frequenti sull'uso del servizio.
- [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/): informazioni e notizie aggiornate sulla sicurezza di Azure.

<!---HONumber=AcomDC_0921_2016-->