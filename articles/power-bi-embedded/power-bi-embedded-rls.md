---
title: Sicurezza a livello di riga con Power BI Embedded
description: Informazioni dettagliate sulla sicurezza a livello di riga con Power BI Embedded
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.assetid: 7936ade5-2c75-435b-8314-ea7ca815867a
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 03/11/2017
ms.author: asaxton
ms.translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: 1cde5b9ee4c716af07d427d4d0eb3f0775d456ac
ms.contentlocale: it-it
ms.lasthandoff: 03/14/2017

---
# <a name="row-level-security-with-power-bi-embedded"></a>Sicurezza a livello di riga con Power BI Embedded

La sicurezza a livello di riga può essere usata per limitare l'accesso utente a dati specifici in un report o a un set di dati, consentendo a più utenti diversi di usare lo stesso report visualizzando al tempo stesso dati diversi. Power BI Embedded supporta ora i set di dati configurati con la sicurezza a livello di riga.

![](media/power-bi-embedded-rls/pbi-embedded-rls-flow-1.png)

Per sfruttare i vantaggi della sicurezza a livello di riga, è importante comprendere tre concetti principali, ovvero utenti, ruoli e regole. Ecco informazioni più approfondite su ogni concetto:

**Utenti** : utenti finali effettivi che visualizzano i report. In Power BI Embedded gli utenti vengono identificati dalla proprietà username nel token dell'app.

**Ruoli** : utenti appartenenti ai ruoli. Un ruolo è un contenitore di regole e può essere denominato ad esempio "Responsabile vendite" o "Rappresentante". In Power BI Embedded gli utenti vengono identificati dalla proprietà roles nel token dell'app.

**Regole** : i ruoli hanno regole e tali regole sono i filtri effettivi che vengono applicati ai dati. È ad esempio possibile usare un filtro semplice come "Paese = USA" o un filtro più dinamico.

### <a name="example"></a>Esempio

Per il resto di questo articolo verrà fornito un esempio di creazione di sicurezza a livello di riga e quindi di utilizzo di tale funzionalità in un'applicazione incorporata. Il nostro esempio usa il file PBIX [Retail Analysis Sample](http://go.microsoft.com/fwlink/?LinkID=780547) .

![](media/power-bi-embedded-rls/pbi-embedded-rls-scenario-2.png)

L'esempio di analisi delle vendite al dettaglio mostra le vendite per tutti i punti vendita di una catena specifica. Senza sicurezza a livello di riga, ogni manager locale che effettua l'acceso e visualizza il report vedrà gli stessi dati. I dirigenti hanno deciso che ogni manager locale deve visualizzare solo le vendite relative ai punti vendita gestiti dal manager specifico e a questo scopo è possibile usare la sicurezza a livello di riga.

La sicurezza a livello di riga viene creata in Power BI Desktop. Quando il set di dati e il report vengono aperti, è possibile passare alla vista diagramma per visualizzare lo schema:

![](media/power-bi-embedded-rls/pbi-embedded-rls-diagram-view-3.png)

Ecco alcuni aspetti dello schema da notare:

* Tutte le misure, ad esempio **Vendite totali**, sono archiviate nella tabella dei fatti **Vendite**.
* Sono presenti altre quattro tabelle relative alle dimensioni correlate, ovvero **Elemento**, **Tempo**, **Negozio** e **Distretto**.
* Le frecce sulle linee relative alle relazioni indicano il modo in cui i filtri possono essere applicati da una tabella a un'altra. Se, ad esempio, si posiziona un filtro su **Tempo[Data]**, nello schema corrente verrebbero filtrati solo i valori della tabella **Vendite**. Questo filtro non influirebbe su altre tabelle, perché tutte le frecce sulle linee relative alle relazioni fanno riferimento alla tabella Sales, non ad altre tabelle.
* La tabella **District** indica il manager per ogni area:
  
  ![](media/power-bi-embedded-rls/pbi-embedded-rls-district-table-4.png)

In base a questo schema, se si applica un filtro alla colonna **Responsabile distretto** nella tabella Distretto e se tale filtro corrisponde all'utente che visualizza il report,verranno filtrate anche le tabelle **Negozio** e **Vendite**solo i dati relativi al responsabile di distretto specifico.

Ecco come:

1. Nella scheda Creazione di modelli fare clic su **Gestisci ruoli**.  
   ![](media/power-bi-embedded-rls/pbi-embedded-rls-modeling-tab-5.png)
2. Creare un nuovo ruolo denominato **Manager**.  
   ![](media/power-bi-embedded-rls/pbi-embedded-rls-manager-role-6.png)
3. Nella tabella **Distretto** immettere l'espressione DAX seguente: **[Responsabile distretto] = USERNAME()**  
   ![](media/power-bi-embedded-rls/pbi-embedded-rls-manager-role-7.png)
4. Per assicurarsi che i ruoli funzionino correttamente, nella scheda **Creazione di modelli** fare clic su **Visualizza come ruoli** quindi immettere i valori seguenti:  
   ![](media/power-bi-embedded-rls/pbi-embedded-rls-view-as-roles-8.png)
   
   I report mostrano ora i dati che verrebbero visualizzati se effettuasse l'accesso l'utente **Andrew Ma**.

Se si applica il filtro come indicato in questa sezione, vengono filtrati tutti i record delle tabelle **Distretto**, **Negozio** e **Vendite**. A causa tuttavia della direzione del filtro nelle relazioni tra **Vendite** e **Tempo** e tra **Vendite** ed **Elemento**, le tabelle **Elemento** e **Tempo** non verranno filtrate.

![](media/power-bi-embedded-rls/pbi-embedded-rls-diagram-view-9.png)

Questo approccio potrebbe essere adatto a questo requisito, ma se non si vuole che i manager visualizzino elementi per cui non presentano vendite, è possibile attivare il filtro incrociato bidirezionale per la relazione e applicare il filtro di sicurezza in entrambe le direzioni. A questo scopo, modificare come indicato di seguito la relazione tra **Vendite** ed **Elemento**:

![](media/power-bi-embedded-rls/pbi-embedded-rls-edit-relationship-10.png)

I filtri possono essere ora applicati anche dalla tabella Sales alla tabella **Item** :

![](media/power-bi-embedded-rls/pbi-embedded-rls-diagram-view-11.png)

> [!NOTE]
> Se si usa la modalità DirectQuery per i dati, sarà necessario abilitare il filtro incrociato bidirezionale selezionando queste due opzioni:

1. **File** -> **Opzioni e impostazioni** -> **Funzionalità di anteprima** -> **Abilita il filtro incrociato in entrambe le direzioni per DirectQuery**.
2. **File** -> **Opzioni e impostazioni** -> **DirectQuery** -> **Consenti misure senza limitazioni in modalità DirectQuery**.

Per altre informazioni sui filtri incrociati bidirezionali, scaricare il white paper [Bidirectional cross-filtering in SQL Server Analysis Services 2016 and Power BI Desktop](http://download.microsoft.com/download/2/7/8/2782DF95-3E0D-40CD-BFC8-749A2882E109/Bidirectional cross-filtering in Analysis Services 2016 and Power BI.docx) (Filtro incrociato bidirezionale in SQL Server Analysis Services 2016 e Power BI Desktop).

Queste sono tutte le operazioni da eseguire in Power BI Desktop, ma per consentire il funzionamento delle regole di sicurezza a livello di riga in Power BI Embedded è necessario ancora un passaggio. Gli utenti vengono autenticati e autorizzati dall'applicazione e i token dell'app vengono usati per concedere l'accesso a un report specifico di Power BI Embedded. Power BI Embedded non ha informazioni specifiche sull'identità dell'utente. Per consentire il funzionamento della sicurezza a livello di riga, sarà necessario passare contesto aggiuntivo come parte del token dell'app:

* **username** (facoltativo): usata con la sicurezza a livello di riga, questa stringa può semplificare l'identificazione dell'utente quando si applicano le regole di sicurezza a livello di riga. Vedere l'articolo relativo all'uso della sicurezza a livello di riga con Power BI Embedded.
* **roles** : stringa contenente i ruoli da selezionare quando si applicano le regole di sicurezza a livello di riga. Se si passa più di un ruolo, è consigliabile passarli come matrice di stringhe.

Per creare il token, usare il metodo [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#Microsoft_PowerBI_Security_PowerBIToken_CreateReportEmbedToken_System_String_System_String_System_String_System_DateTime_System_String_System_Collections_Generic_IEnumerable_System_String__). Se la proprietà username è presente, è necessario passare anche almeno un valore in roles.

È possibile ad esempio modificare EmbedSample. La riga 55 di DashboardController può essere aggiornata da

    var embedToken = PowerBIToken.CreateReportEmbedToken(this.workspaceCollection, this.workspaceId, report.Id);

to

    var embedToken = PowerBIToken.CreateReportEmbedToken(this.workspaceCollection, this.workspaceId, report.Id, "Andrew Ma", ["Manager"]);'

Il token dell'app completo avrà un aspetto analogo al seguente:

![](media/power-bi-embedded-rls/pbi-embedded-rls-app-token-string-12.png)

Al termine di queste procedure, se un utente accede all'applicazione per visualizzare il report, potrà vedere solo i dati che è autorizzato a vedere, in base a quanto definito dalla sicurezza a livello di riga.

![](media/power-bi-embedded-rls/pbi-embedded-rls-dashboard-13.png)

## <a name="see-also"></a>Vedere anche

[Sicurezza a livello di riga con Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-admin-rls/)  
[Autenticazione e autorizzazione con Power BI Embedded](power-bi-embedded-app-token-flow.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[Esempio di incorporamento con JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
Altre domande? [Contattare la community di Power BI](http://community.powerbi.com/)


