---
title: Sicurezza a livello di riga con le raccolte di aree di lavoro di Power BI
description: Informazioni dettagliate sulla sicurezza a livello di riga con le raccolte di aree di lavoro di Power BI
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ROBOTS: NOINDEX
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: asaxton
ms.openlocfilehash: 8c3ce8bc69a098d3133f27a2604f9d564693ea54
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="row-level-security-with-power-bi-workspace-collections"></a>Sicurezza a livello di riga con le raccolte di aree di lavoro di Power BI

La sicurezza a livello di riga può essere usata per limitare l'accesso utente a dati specifici in un report o a un set di dati, consentendo a più utenti diversi di usare lo stesso report visualizzando al tempo stesso dati diversi. Le raccolte di aree di lavoro di Power BI supportano i set di dati configurati con Sicurezza a livello di riga.

![Flusso di Sicurezza a livello di riga con le raccolte di aree di lavoro di Power BI](media/row-level-security/flow-1.png)

> [!IMPORTANT]
> Le raccolte di aree di lavoro di Power BI sono deprecate e sono disponibili fino a giugno 2018 o fino alla data specificata nel contratto. È consigliabile pianificare la migrazione a Power BI Embedded per evitare interruzioni nell'applicazione. Per informazioni su come eseguire la migrazione dei dati a Power BI Embedded, vedere [Come eseguire la migrazione del contenuto delle raccolte di aree di lavoro di Power BI a Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Per sfruttare i vantaggi della sicurezza a livello di riga, è importante comprendere tre concetti principali, ovvero utenti, ruoli e regole. Ecco informazioni più approfondite su ogni concetto:

**Utenti** : utenti finali effettivi che visualizzano i report. Nelle raccolte di aree di lavoro di Power BI gli utenti vengono identificati dalla proprietà username nel token dell'app.

**Ruoli** : utenti appartenenti ai ruoli. Un ruolo è un contenitore di regole a cui può essere assegnato un nome quale "Responsabile vendite" o "Rappresentante". Nelle raccolte di aree di lavoro di Power BI gli utenti vengono identificati dalla proprietà roles nel token dell'app.

**Regole** : i ruoli hanno regole e tali regole sono i filtri effettivi che vengono applicati ai dati. È ad esempio possibile usare un filtro semplice come "Paese = USA" o un filtro più dinamico.

### <a name="example"></a>Esempio

Nel resto di questo articolo viene illustrato un esempio di creazione di Sicurezza a livello di riga con un esempio di utilizzo di tale funzionalità in un'applicazione incorporata. Il nostro esempio usa il file PBIX [Retail Analysis Sample](http://go.microsoft.com/fwlink/?LinkID=780547) .

![Report delle vendite di esempio](media/row-level-security/scenario-2.png)

L'esempio di analisi delle vendite al dettaglio mostra le vendite per tutti i punti vendita di una catena specifica. Senza Sicurezza a livello di riga, tutti i manager locali che effettuano l'accesso e visualizzano il report vedono gli stessi dati. I dirigenti hanno deciso che ogni manager locale deve visualizzare solo le vendite relative ai punti vendita gestiti dal manager specifico e a questo scopo è possibile usare la sicurezza a livello di riga.

La sicurezza a livello di riga viene creata in Power BI Desktop. Quando il set di dati e il report vengono aperti, è possibile passare alla vista diagramma per visualizzare lo schema:

![Diagramma del modello in Power BI Desktop](media/row-level-security/diagram-view-3.png)

Ecco alcuni aspetti dello schema da notare:

* Tutte le misure, ad esempio **Vendite totali**, sono archiviate nella tabella dei fatti **Vendite**.
* Sono presenti altre quattro tabelle relative alle dimensioni correlate, ovvero **Elemento**, **Tempo**, **Negozio** e **Distretto**.
* Le frecce sulle linee relative alle relazioni indicano il modo in cui i filtri possono essere applicati da una tabella a un'altra. Se, ad esempio, si posiziona un filtro su **Tempo[Data]**, nello schema corrente verrebbero filtrati solo i valori della tabella **Vendite**. Questo filtro non influirebbe su altre tabelle, perché tutte le frecce sulle linee relative alle relazioni fanno riferimento alla tabella Sales, non ad altre tabelle.
* La tabella **District** indica il manager per ogni area:
  
  ![Righe della tabella District](media/row-level-security/district-table-4.png)

In base a questo schema, se si applica un filtro alla colonna **District Manager** nella tabella District e se tale filtro corrisponde all'utente che visualizza il report, vengono filtrate anche le tabelle **Store** e **Sales** in modo che siano visualizzati solo i dati relativi al responsabile di distretto specifico.

Ecco come:

1. Nella scheda Creazione di modelli fare clic su **Gestisci ruoli**.  
   ![Pulsante Gestisci ruoli nella barra multifunzione Creazione di modelli](media/row-level-security/modeling-tab-5.png)
2. Creare un nuovo ruolo denominato **Manager**.  
   ![Creazione di ruoli in Power BI Desktop](media/row-level-security/manager-role-6.png)
3. Nella tabella **Distretto** immettere l'espressione DAX seguente: **[Responsabile distretto] = USERNAME()**  
   ![Espressione filtro DAX per la tabella nel ruolo](media/row-level-security/manager-role-7.png)
4. Per assicurarsi che i ruoli funzionino correttamente, nella scheda **Creazione di modelli** fare clic su **Visualizza come ruoli** quindi immettere i valori seguenti:  
   ![Visualizza come ruoli](media/row-level-security/view-as-roles-8.png)

   I report mostrano ora i dati visualizzati per l'utente **Andrew Ma**.

Se si applica il filtro come indicato in questa sezione, vengono filtrati tutti i record delle tabelle **District**, **Store** e **Sales**. A causa tuttavia della direzione del filtro nelle relazioni tra **Vendite** e **Tempo** e tra **Vendite** ed **Elemento**, le tabelle **Elemento** e **Tempo** non verranno filtrate.

![Vista diagramma con evidenziazione delle relazioni](media/row-level-security/diagram-view-9.png)

Questo approccio potrebbe essere adatto a questo requisito, ma se non si vuole che i manager visualizzino elementi per cui non presentano vendite, è possibile attivare il filtro incrociato bidirezionale per la relazione e applicare il filtro di sicurezza in entrambe le direzioni. A questo scopo, modificare come indicato di seguito la relazione tra **Vendite** ed **Elemento**:

![Direzione del filtro incrociato per la relazione](media/row-level-security/edit-relationship-10.png)

I filtri possono essere ora applicati anche dalla tabella Sales alla tabella **Item** :

![Icona della direzione del filtro della relazione nella vista diagramma](media/row-level-security/diagram-view-11.png)

> [!NOTE]
> Se per i dati si usa la modalità DirectQuery, è necessario abilitare il filtro incrociato bidirezionale selezionando le due opzioni seguenti:

1. **File** -> **Opzioni e impostazioni** -> **Funzionalità di anteprima** -> **Abilita il filtro incrociato in entrambe le direzioni per DirectQuery**.
2. **File** -> **Opzioni e impostazioni** -> **DirectQuery** -> **Consenti misure senza limitazioni in modalità DirectQuery**.

Per altre informazioni sui filtri incrociati bidirezionali, scaricare il white paper [Bidirectional cross-filtering in SQL Server Analysis Services 2016 and Power BI Desktop](http://download.microsoft.com/download/2/7/8/2782DF95-3E0D-40CD-BFC8-749A2882E109/Bidirectional cross-filtering in Analysis Services 2016 and Power BI.docx) (Filtro incrociato bidirezionale in SQL Server Analysis Services 2016 e Power BI Desktop).

Queste sono tutte le operazioni da eseguire in Power BI Desktop, ma per consentire il funzionamento delle regole di sicurezza a livello di riga in Power BI Embedded è necessario ancora un passaggio. Gli utenti vengono autenticati e autorizzati dall'applicazione e i token dell'app vengono usati per concedere l'accesso a un report specifico di Power BI Embedded. Power BI Embedded non ha informazioni specifiche sull'identità dell'utente. Per consentire il funzionamento di Sicurezza a livello di riga, è necessario passare contesto aggiuntivo all'interno del token dell'app:

* **username** (facoltativo): usata con la sicurezza a livello di riga, questa stringa può semplificare l'identificazione dell'utente quando si applicano le regole di sicurezza a livello di riga. Vedere l'articolo relativo all'uso della sicurezza a livello di riga con Power BI Embedded.
* **roles** : stringa contenente i ruoli da selezionare quando si applicano le regole di sicurezza a livello di riga. Se si passa più di un ruolo, è consigliabile passarli come matrice di stringhe.

Per creare il token, usare il metodo [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#Microsoft_PowerBI_Security_PowerBIToken_CreateReportEmbedToken_System_String_System_String_System_String_System_DateTime_System_String_System_Collections_Generic_IEnumerable_System_String__). Se la proprietà username è presente, è necessario passare anche almeno un valore in roles.

È possibile ad esempio modificare EmbedSample. La riga 55 di DashboardController può essere aggiornata da

    var embedToken = PowerBIToken.CreateReportEmbedToken(this.workspaceCollection, this.workspaceId, report.Id);

to

    var embedToken = PowerBIToken.CreateReportEmbedToken(this.workspaceCollection, this.workspaceId, report.Id, "Andrew Ma", ["Manager"]);'

Il token dell'app completo ha un aspetto analogo al seguente:

![Esempio di token Web JSON](media/row-level-security/app-token-string-12.png)

Al termine di queste procedure, se un utente accede all'applicazione per visualizzare il report, visualizza i dati che è autorizzato a vedere, in base a quanto definito da Sicurezza a livello di riga.

![Report visualizzato nell'applicazione](media/row-level-security/dashboard-13.png)

## <a name="see-also"></a>Vedere anche 

[Sicurezza a livello di riga con Power BI](https://powerbi.microsoft.com/documentation/powerbi-admin-rls/)  
[Autenticazione e autorizzazione con le raccolte di aree di lavoro di Power BI](app-token-flow.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[Esempio di incorporamento con JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  

Altre domande? [Contattare la community di Power BI](http://community.powerbi.com/)