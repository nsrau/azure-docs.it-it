---
title: Come eseguire la migrazione del contenuto delle raccolte di aree di lavoro di Power BI a Power BI Embedded | Microsoft Docs
description: Informazioni su come eseguire la migrazione dalle raccolte di aree di lavoro di Power BI a Power BI Embedded e sfruttare i miglioramenti per l'incorporamento nelle app.
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/28/2017
ms.author: asaxton
ms.openlocfilehash: 069f31c8213bd0d8586f7ca50e543acfdad8a2b3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-migrate-power-bi-workspace-collection-content-to-power-bi-embedded"></a>Come eseguire la migrazione del contenuto delle raccolte di aree di lavoro di Power BI a Power BI Embedded

Informazioni su come eseguire la migrazione dalle raccolte di aree di lavoro di Power BI a Power BI Embedded. Questo articolo illustra come eseguire la migrazione dalle raccolte di aree di lavoro di Azure Power BI a Power BI Embedded. Descrive anche le modifiche previste per le applicazioni.

La risorsa Raccolte di aree di lavoro di Power BI continuerà a essere disponibile per un periodo di tempo limitato dopo il rilascio di Power BI Premium con disponibilità generale. I clienti con contratto Enterprise avranno accesso alle raccolte di aree di lavoro esistenti fino alla scadenza del contratto. I clienti che hanno acquistato la risorsa Raccolte di aree di lavoro di Power BI tramite canali diretti o CSP potranno accedere per un anno a Power BI Premium con disponibilità generale.

> [!IMPORTANT]
> Anche se la migrazione è dipendente dal servizio Power BI, la dipendenza da Power BI non esiste per gli utenti dell'applicazione quando si usa un **token di incorporamento**. Gli utenti non devono effettuare l'iscrizione a Power BI per visualizzare il contenuto incorporato nell'applicazione. È possibile usare questo approccio per incorporare Power BI per i clienti.

![Flusso di Power BI Embedded](media/migrate-from-power-bi-workspace-collections/powerbi-embed-flow.png)

## <a name="prepare-for-the-migration"></a>Preparare la migrazione

È necessario eseguire alcune attività preliminari per la migrazione dal servizio Raccolte di aree di lavoro di Power BI a Power BI Embedded. È necessario avere a disposizione un tenant e un utente con licenza di Power BI Pro.

1. Accertarsi di avere accesso a un tenant Azure Active Directory (Azure AD).

    Quale tenant usare?

    * Usare il tenant Power BI aziendale esistente?
    * Usare un tenant separato per l'applicazione?
    * Usare un tenant separato per ogni cliente?

    Se si decide di creare un nuovo tenant per l'applicazione o per ogni cliente, vedere uno degli articoli seguenti:

    * [Creare un tenant di Azure Active Directory](https://powerbi.microsoft.com/documentation/powerbi-developer-create-an-azure-active-directory-tenant/)
    * [Come ottenere un tenant di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-howto-tenant).

2. In questo nuovo tenant creare un utente che funga da account "master" dell'applicazione. Tale account dovrà effettuare l'iscrizione a Power BI e avere una licenza di Power BI Pro assegnata.

## <a name="accounts-within-azure-ad"></a>Account in Azure AD

Nel tenant devono esistere gli account seguenti.

> [!NOTE]
> Questi account devono avere licenze di Power BI Pro per usare le aree di lavoro per le app.

1. Un utente amministratore tenant.

    È consigliabile che l'area di lavoro per le app per l'incorporamento abbia l'amministratore tenant elencato come membro.

2. Account per gli analisti che creano il contenuto.

    Questi utenti devono essere assegnati alle aree di lavoro per le app in base alle esigenze.

3. Un account utente *master* dell'applicazione o un account di servizio.

    Il back-end dell'applicazione archivia le credenziali per questo account. Usare l'account *master* per acquisire un token di Azure AD da usare con le API REST di Power BI. Questo account viene usato per generare il token di incorporamento per l'applicazione. L'account *master* deve essere un amministratore delle aree di lavoro per le app create per l'incorporamento.

    **Questo account è un normale account utente dell'organizzazione usato per l'incorporamento.**

## <a name="app-registration-and-permissions"></a>Registrazione e autorizzazioni dell'app

Per eseguire chiamate API REST, registrare un'applicazione con Azure AD. Nel portale di Microsoft Azure sono disponibili altre opzioni di configurazione oltre alla pagina di registrazione delle app di Power BI. Per altre informazioni, vedere [Registrare un'app di Azure AD per incorporare il contenuto di Power BI](https://powerbi.microsoft.com/documentation/powerbi-developer-register-app/).

È consigliabile registrare l'applicazione usando l'account **master** dell'applicazione stessa.

## <a name="create-app-workspaces-required"></a>Creare aree di lavoro per le app (obbligatorio)

Se l'applicazione è destinata a più clienti, è possibile sfruttare le aree di lavoro per le app per fornire un migliore isolamento. Dashboard e report saranno così isolati tra un cliente e l'altro. È quindi possibile usare un account Power BI per ogni area di lavoro per le app per isolare ulteriormente le esperienze dell'applicazione tra un cliente e l'altro, con la possibilità di usare un solo account per semplicità.

> [!IMPORTANT]
> Non è possibile usare un'area di lavoro personale per sfruttare l'incorporamento per i clienti.

È necessario un utente con licenza Pro per creare un'area di lavoro per le app in Power BI. L'utente di Power BI che crea l'area di lavoro per le app è un amministratore di tale area di lavoro per impostazione predefinita. **L'account *master* dell'applicazione deve essere un amministratore dell'area di lavoro.**

## <a name="content-migration"></a>Migrazione del contenuto

La migrazione del contenuto dalle raccolte di aree di lavoro a Power BI Embedded può essere eseguita in parallelo alla soluzione corrente e non richiede alcun tempo di inattività.

È disponibile uno **strumento di migrazione** che è possibile usare a supporto della copia del contenuto dalle raccolte di aree di lavoro di Power BI a Power BI Embedded. Lo strumento è utile in particolare se si hanno molti report. Per altre informazioni, vedere [Strumento di migrazione di Power BI Embedded](migrate-tool.md).

La migrazione del contenuto si basa principalmente su due API.

1. Download PBIX: questa API può scaricare i file PBIX che sono stati caricati in Power BI dopo ottobre 2016.
2. Importazione PBIX: questa API carica i file PBIX in Power BI.

Per alcuni frammenti di codice correlati, vedere [Frammenti di codice per eseguire la migrazione del contenuto da Power BI Embedded](migrate-code-snippets.md).

### <a name="report-types"></a>Tipi di report

Esistono diversi tipi di report, ognuno dei quali richiede un flusso di migrazione diverso.

#### <a name="cached-dataset-and-report"></a>Set di dati e report memorizzati nella cache

I set di dati memorizzati nella cache si riferiscono a file PBIX con dati importati anziché a una connessione dinamica o DirectQuery.

**Flusso**

1. Chiamare l'API Download PBIX dall'area di lavoro della raccolta di aree di lavoro di Power BI.
2. Salvare il file PBIX.
3. Chiamare l'API Importazione PBIX per l'area di lavoro di Power BI Embedded.

#### <a name="directquery-dataset-and-report"></a>Set di dati e report DirectQuery

**Flusso**

1. Chiamare GET https://api.powerbi.com/v1.0/collections/{collection_id}/workspaces/{wid}/datasets/{dataset_id}/Default.GetBoundGatewayDataSources e salvare la stringa di connessione ricevuta.
2. Chiamare l'API Download PBIX dall'area di lavoro della raccolta di aree di lavoro di Power BI.
3. Salvare il file PBIX.
4. Chiamare l'API Importazione PBIX per l'area di lavoro di Power BI Embedded.
5. Aggiornare la stringa di connessione chiamando - POST https://api.powerbi.com/v1.0/myorg/datasets/{dataset_id}/Default.SetAllConnections
6. Ottenere ID GW e ID origine dati chiamando - GET https://api.powerbi.com/v1.0/myorg/datasets/{dataset_id}/Default.GetBoundGatewayDataSources
7. Aggiornare le credenziali dell'utente chiamando - PATCH https://api.powerbi.com/v1.0/myorg/gateways/{gateway_id}/datasources/{datasource_id}

#### <a name="old-dataset-and-reports"></a>Set di dati e report precedenti

I report caricati prima di ottobre 2016 non supportano la funzionalità di Download PBIX.

**Flusso**

1. Ottenere il file PBIX dall'ambiente di sviluppo (controllo del codice sorgente interno).
2. Chiamare l'API Importazione PBIX per l'area di lavoro di Power BI Embedded.

#### <a name="push-dataset-and-report"></a>Eseguire il push di un set di dati e un report

La funzione Download PBIX non supporta i set di dati dell'*API push*. I set di dati dell'API Push non possono essere trasferiti dalle raccolte di aree di lavoro di Power BI a Power BI Embedded.

**Flusso**

1. Chiamare l'API "Create dataset" con il formato JSON del set di dati per creare un set di dati per l'area di lavoro di Power BI Embedded.
2. Ricreare il report per il set di dati creato*.

Con alcune soluzioni alternative è possibile eseguire la migrazione del report dell'API Push dalle raccolte di aree di lavoro di Power BI a Power BI Embedded provando le operazioni seguenti:

1. Caricare un file PBIX fittizio nell'area di lavoro della raccolta di aree di lavoro di Power BI.
2. Clonare il report dell'API Push e associarlo al file PBIX fittizio del passaggio 1.
3. Scaricare il report dell'API Push con il file PBIX fittizio.
4. Caricare il file PBIX fittizio nell'area di lavoro di Power BI Embedded.
5. Creare un set di dati push nell'area di lavoro di Power BI Embedded.
6. Riassociare il report al set di dati dell'API Push.

## <a name="create-and-upload-new-reports"></a>Creare e caricare nuovi report

Oltre al contenuto trasferito dalle raccolte di aree di lavoro di Power BI, è possibile creare report e set di dati con Power BI Desktop e quindi pubblicare i report in un'area di lavoro per le app. L'utente finale che pubblica i report deve avere una licenza di Power BI Pro per eseguire la pubblicazione in un'area di lavoro per le app.

## <a name="rebuild-your-application"></a>Ricompilare l'applicazione

1. Modificare l'applicazione per usare le API REST di Power BI e il percorso dei report in powerbi.com.

2. Ricompilare l'autenticazione AuthN/AuthZ con l'account *master* dell'applicazione. È possibile usare un [token di incorporamento](https://msdn.microsoft.com/library/mt784614.aspx) per consentire all'utente di agire per conto di altri utenti.

3. Incorporare i report di Power BI Embedded nell'applicazione. Per altre informazioni, vedere [Come incorporare i dashboard, i report e i riquadri di Power BI](https://powerbi.microsoft.com/documentation/powerbi-developer-embedding-content/).

## <a name="map-your-users-to-a-power-bi-user"></a>Eseguire il mapping degli utenti a un utente di Power BI

All'interno dell'applicazione, si esegue il mapping degli utenti gestiti nell'applicazione stessa a un account Power BI *master*. Le credenziali di questo account Power BI *master* vengono archiviate nell'applicazione e usate per creare token di incorporamento.

## <a name="what-to-do-when-you-are-ready-for-production"></a>Operazioni da eseguire quando si è pronti per la produzione

Quando si è pronti per passare alla produzione, è necessario eseguire queste operazioni:

- Se si usa un tenant separato per lo sviluppo, è necessario assicurarsi che le aree di lavoro per le app, oltre ai dashboard e report, siano disponibili nell'ambiente di produzione. Assicurarsi di aver creato l'applicazione in Azure AD per il tenant di produzione e assegnato le autorizzazioni per l'app come indicato nel passaggio 1.

- Acquistare una capacità adatta alle proprie esigenze. È possibile usare il [white paper sulla pianificazione della capacità di analisi incorporata](https://aka.ms/pbiewhitepaper) per determinare la capacità necessaria. Quando si è pronti, è possibile acquistare una risorsa Power BI Embedded nel portale di Azure.

- Modificare l'area di lavoro per le app e assegnarla a una capacità in Avanzate.

    ![Assegnare l'area di lavoro per le app a una capacità in powerbi.com](media/migrate-from-power-bi-workspace-collections/embedded-capacity.png)

- Distribuire l'applicazione aggiornata nell'ambiente di produzione e iniziare a incorporare report da Power BI Embedded.

## <a name="after-migration"></a>Dopo la migrazione

Sono necessarie alcune attività di pulizia nella risorsa Raccolte di aree di lavoro di Power BI.

- Rimuovere tutte le aree di lavoro dalla soluzione distribuita nel servizio Raccolte di aree di lavoro di Power BI di Azure.
- Eliminare tutte le raccolte di aree di lavoro esistenti in Azure.

## <a name="next-steps"></a>Passaggi successivi

A questo punto verrà eseguita la migrazione dell'applicazione a Power BI Embedded. Per informazioni su come incorporare i dashboard di Power BI, vedere [Come incorporare i dashboard, i report e i riquadri di Power BI](https://powerbi.microsoft.com/documentation/powerbi-developer-embedding-content/).

Altre domande? [Contattare la community di Power BI](http://community.powerbi.com/)