---
title: Uso di Azure AD Connect Health per la sincronizzazione | Documentazione Microsoft
description: "Questa è la pagina di Azure AD Connect Health in cui viene descritto come monitorare  il Servizio di sincronizzazione Azure AD Connect."
services: active-directory
documentationcenter: 
author: karavar
manager: femila
ms.assetid: 1dfbeaba-bda2-4f68-ac89-1dbfaf5b4015
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/18/2017
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4b06338cb62cc458e7b097db36023f0746d4e969
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-azure-ad-connect-sync-with-azure-ad-connect-health"></a>Monitorare la sincronizzazione di Azure AD Connect con Azure AD Connect Health
La documentazione seguente è specifica per il monitoraggio di Azure Active Directory Connect (Sincronizzazione) con Azure AD Connect Health.  Per informazioni sul monitoraggio di AD FS con Azure AD Connect Health, vedere [Uso di Azure AD Connect Health con AD FS](active-directory-aadconnect-health-adfs.md). Per informazioni sul monitoraggio di Servizi di dominio Active Directory con Azure AD Connect Health, vedere [Uso di Azure AD Connect Health con Servizi di dominio Active Directory](active-directory-aadconnect-health-adds.md).

![Azure AD Connect Health per la sincronizzazione](./media/active-directory-aadconnect-health-sync/sync-blade.png)

## <a name="alerts-for-azure-ad-connect-health-for-sync"></a>Avvisi per Azure AD Connect Health per la sincronizzazione
La sezione Avvisi di Azure AD Connect Health contiene l'elenco degli avvisi attivi. Ogni avviso include informazioni pertinenti, la procedura di risoluzione e collegamenti alla documentazione correlata. Selezionando un avviso attivo o risolto, verrà visualizzato un nuovo pannello con altre informazioni, oltre ai passaggi che è possibile eseguire per risolvere l'avviso e i collegamenti ad altri documenti. È anche possibile visualizzare dati storici sugli avvisi risolti in passato.

Selezionando un avviso, vengono fornite informazioni aggiuntive, nonché la procedura per risolvere l'avviso e collegamenti ad altra documentazione.

![Errore del servizio di sincronizzazione Azure AD Connect](./media/active-directory-aadconnect-health-sync/alert.png)

### <a name="limited-evaluation-of-alerts"></a>Valutazione limitata degli avvisi
Se Azure AD Connect NON usa la configurazione predefinita (ad esempio, se il filtro attributi è passato dalla configurazione predefinita a una configurazione personalizzata), l'agente Azure AD Connect Health non caricherà gli eventi di errore correlati ad Azure AD Connect.

La valutazione degli avvisi da parte del servizio è quindi soggetta a limiti. Verrà visualizzato un banner indicante questa condizione nel portale di Azure sotto il servizio.

![Azure AD Connect Health per la sincronizzazione](./media/active-directory-aadconnect-health-sync/banner.png)

Per modificare questa condizione, fare clic su "Impostazioni" e consentire all'agente Azure AD Connect Health di caricare tutti i log degli errori.

![Azure AD Connect Health per la sincronizzazione](./media/active-directory-aadconnect-health-sync/banner2.png)

## <a name="sync-insight"></a>Informazioni dettagliate sulla sincronizzazione
Gli amministratori in genere vogliono conoscere il tempo richiesto per sincronizzare le modifiche in Azure AD e la quantità di modifiche apportate. Questa funzionalità consente di visualizzare facilmente queste informazioni usando i grafici seguenti:   

* Latenza delle operazioni di sincronizzazione
* Tendenza nelle modifiche agli oggetti

### <a name="sync-latency"></a>Latenza della sincronizzazione
Questa funzionalità fornisce una tendenza grafica della latenza delle operazioni di sincronizzazione, importazione, esportazione e così via, per i connettori.  È così disponibile un modo semplice e rapido per conoscere non solo la latenza delle operazioni (che è superiore in caso di set di modifiche esteso), ma anche per rilevare le anomalie a livello di latenza che potrebbero richiedere approfondimenti.

![Latenza della sincronizzazione](./media/active-directory-aadconnect-health-sync/synclatency02.png)

Per impostazione predefinita, viene visualizzata solo la latenza dell'operazione di esportazione per il connettore Azure AD.  Per visualizzare altre operazioni nel connettore o le operazioni di altri connettori, fare clic con il pulsante destro del mouse sul grafico e scegliere Modifica grafico oppure fare clic sul pulsante "Modifica il grafico sulla latenza" e scegliere l'operazione e i connettori specifici.

### <a name="sync-object-changes"></a>Sincronizzazione delle modifiche agli oggetti
Questa funzionalità fornisce una tendenza grafica del numero di modifiche valutate ed esportate in Azure AD.  Attualmente, è difficile provare a raccogliere informazioni dai log di sincronizzazione.  Il grafico fornisce non solo un modo più semplice per monitorare il numero di modifiche in corso nell'ambiente, ma anche una panoramica visiva degli errori che si sono verificati.

![Latenza della sincronizzazione](./media/active-directory-aadconnect-health-sync/syncobjectchanges02.png)

## <a name="object-level-synchronization-error-report-preview"></a>Report degli errori di sincronizzazione a livello di oggetto (anteprima)
Questa funzionalità offre un report sugli errori di sincronizzazione che possono verificarsi durante la sincronizzazione dei dati relativi alle identità tra Active Directory di Windows Server e Azure AD con Azure AD Connect.

* Il report contiene gli errori registrati dal client di sincronizzazione (Azure AD Connect versione 1.1.281.0 o successiva).
* Include gli errori verificatisi nell'ultima operazione di sincronizzazione nel motore di sincronizzazione (operazione di esportazione nel connettore Azure AD).
* Affinché il report includa i dati più recenti, l'agente di Azure AD Connect Health per la sincronizzazione deve avere la connettività in uscita agli endpoint necessari.
* Il report viene **aggiornato ogni 30 minuti** usando i dati caricati dall'agente di Azure AD Connect Health per la sincronizzazione. Offre le funzionalità chiave seguenti:

  * Categorizzazione degli errori
  * Elenco degli oggetti con errore per categoria
  * Tutti i dati sugli errori in un'unica posizione
  * Confronto affiancato degli oggetti con errore a causa di un conflitto
  * Download del report degli errori in formato CSV (presto disponibile)

### <a name="categorization-of-errors"></a>Categorizzazione degli errori
Il report suddivide gli errori di sincronizzazione esistenti nelle categorie seguenti:

| Categoria | Descrizione |
| --- | --- |
| Attributo duplicato |Errori che si verificano quando Azure AD Connect prova a creare o aggiornare oggetti con valori duplicati per uno o più attributi di Azure AD che devono essere univoci in un tenant, come proxyAddresses o UserPrincipalName. |
| Dati non corrispondenti |Errori di sincronizzazione che si verificano quando la funzionalità di corrispondenza flessibile non riesce a trovare una corrispondenza con l'oggetto. |
| Errore di convalida dei dati |Errori causati da dati non validi, ad esempio caratteri non supportati in attributi critici come UserPrincipalName o errori di formato che non superano la convalida prima della scrittura in Azure AD. |
| Attributo di grandi dimensioni |Errori che si verificano quando uno o più attributi superano le dimensioni, la lunghezza o il numero consentito. |
| Altri |Tutti gli altri errori che non rientrano nelle categorie precedenti. Questa categoria verrà suddivisa in sottocategorie in base ai commenti e suggerimenti. |

![Riepilogo del report degli errori di sincronizzazione](./media/active-directory-aadconnect-health-sync/errorreport01.png)
![Categorie del report degli errori di sincronizzazione](./media/active-directory-aadconnect-health-sync/errorreport02.png)

### <a name="list-of-objects-with-error-per-category"></a>Elenco degli oggetti con errore per categoria
Analizzando ogni categoria, verrà visualizzato l'elenco degli oggetti interessati dall'errore della categoria.
![Elenco del report degli errori di sincronizzazione](./media/active-directory-aadconnect-health-sync/errorreport03.png)

### <a name="error-details"></a>Dettagli errore
Nella visualizzazione dettagliata relativa a ogni errore sono disponibili i dati seguenti:

* Identificatori dell'*oggetto AD* coinvolto
* Identificatori dell'*oggetto Azure AD* coinvolto (a seconda dei casi)
* Descrizione dell'errore e istruzioni per la risoluzione
* Articoli correlati

![Dettagli del report degli errori di sincronizzazione](./media/active-directory-aadconnect-health-sync/errorreport04.png)

### <a name="download-the-error-report-as-csv"></a>Download del report degli errori in formato CSV
La selezione del pulsante "Esporta" consente di scaricare un file CSV con tutti i dettagli relativi a tutti gli errori.

## <a name="related-links"></a>Collegamenti correlati
* [Risoluzione degli errori durante la sincronizzazione](../connect/active-directory-aadconnect-troubleshoot-sync-errors.md)
* [Resilienza degli attributi duplicati](../connect/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md)
* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Installazione dell'agente di Azure AD Connect Health](active-directory-aadconnect-health-agent-install.md)
* [Operazioni di Azure AD Connect Health](active-directory-aadconnect-health-operations.md)
* [Uso di Azure AD Connect Health con AD FS](active-directory-aadconnect-health-adfs.md)
* [Uso di Azure AD Connect Health con Servizi di dominio Active Directory](active-directory-aadconnect-health-adds.md)
* [Domande frequenti su Azure AD Connect Health](active-directory-aadconnect-health-faq.md)
* [Cronologia delle versioni di Azure AD Connect Health](active-directory-aadconnect-health-version-history.md)