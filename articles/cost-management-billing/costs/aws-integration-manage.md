---
title: Gestire i costi e l'utilizzo di AWS in Gestione costi di Azure
description: Questo articolo illustra come usare l'analisi dei costi e i budget in Gestione costi per gestire i costi e l'utilizzo di AWS.
author: bandersmsft
ms.author: banders
ms.date: 02/12/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: matrive
ms.custom: ''
ms.openlocfilehash: fd4d4a16642e764276d49dd732a785be48197d01
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "77200028"
---
# <a name="manage-aws-costs-and-usage-in-azure"></a>Gestire i costi e l'utilizzo di AWS in Azure

Dopo aver impostato e configurato l'integrazione del report di costi e utilizzo di AWS per Gestione costi di Azure, è possibile iniziare a gestire i costi e l'utilizzo di AWS. Questo articolo illustra come usare l'analisi dei costi e i budget in Gestione costi per gestire i costi e l'utilizzo di AWS.

Se l'integrazione non è stata ancora configurata, vedere [Impostare e configurare l'integrazione del report di costi e utilizzo di AWS](aws-integration-set-up-configure.md).

_Prima di iniziare_: se non si ha familiarità con l'analisi dei costi, vedere la guida di avvio rapido [Esplorare e analizzare i costi con l'analisi dei costi](quick-acm-cost-analysis.md). Se non si ha familiarità con i budget in Azure, vedere l'esercitazione [Creare e gestire budget di Azure](tutorial-acm-create-budgets.md).

## <a name="view-aws-costs-in-cost-analysis"></a>Visualizzare i costi di AWS nell'analisi dei costi

I costi di AWS sono disponibili in Analisi dei costi negli ambiti seguenti:

- Account AWS collegati in un gruppo di gestione
- Costi di un account AWS collegato
- Costi di un account AWS consolidato

Nelle sezioni successive viene descritto come usare gli ambiti in modo da vedere i dati relativi ai costi e all'utilizzo per ognuno di essi.

### <a name="view-aws-linked-accounts-under-a-management-group"></a>Visualizzare account AWS collegati in un gruppo di gestione

La visualizzazione dei costi tramite l'ambito del gruppo di gestione è l'unico modo per vedere i costi aggregati provenienti da sottoscrizioni e account collegati diversi. L'uso di un gruppo di gestione consente una visualizzazione tra cloud.

In Analisi dei costi aprire la selezione ambiti e selezionare il gruppo di gestione contenente gli account AWS collegati. Di seguito è riportata un'immagine di esempio nel portale di Azure:

![Esempio della visualizzazione Seleziona ambito](./media/aws-integration-manage/select-scope01.png)



Di seguito è riportato un esempio in cui sono indicati i costi del gruppo di gestione in Analisi dei costi, raggruppati per provider (Azure e AWS).

![Esempio che illustra i costi di Azure e AWS per un trimestre in Analisi dei costi](./media/aws-integration-manage/cost-analysis-aws-azure.png)

### <a name="view-aws-linked-account-costs"></a>Visualizzare i costi di un account AWS collegato

Per visualizzare i costi di un account AWS collegato, aprire la selezione ambiti e selezionare l'account AWS collegato. Si noti che gli account collegati sono associati a un gruppo di gestione, come definito nel connettore AWS.

Di seguito è riportato un esempio che illustra la selezione dell'ambito di un account AWS collegato.

![Esempio della visualizzazione Seleziona ambito](./media/aws-integration-manage/select-scope02.png)



### <a name="view-aws-consolidated-account-costs"></a>Visualizzare i costi di un account AWS consolidato

Per visualizzare i costi di un account AWS consolidato, aprire la selezione ambiti e selezionare l'account AWS consolidato. Di seguito è riportato un esempio che illustra la selezione dell'ambito di un account AWS consolidato.

![Esempio della visualizzazione Seleziona ambito](./media/aws-integration-manage/select-scope03.png)



Questo ambito offre una visualizzazione aggregata di tutti gli account AWS collegati associati all'account AWS consolidato. Di seguito è riportato un esempio in cui sono indicati i costi per un account AWS consolidato, raggruppati per nome di servizio.

![Esempio che illustra i costi consolidati di AWS in Analisi dei costi](./media/aws-integration-manage/cost-analysis-aws-consolidated.png)

### <a name="dimensions-available-for-filtering-and-grouping"></a>Dimensioni disponibili per il filtraggio e il raggruppamento

La tabella seguente descrive le dimensioni disponibili in base alle quali raggruppare e filtrare elementi in Analisi dei costi.

| Dimension | Intestazione del report di costi e utilizzo di Amazon | Ambiti | Commenti |
| --- | --- | --- | --- |
| Zona di disponibilità | lineitem/AvailabilityZone | Tutti |   |
| Location | product/Region | Tutti |   |
| Contatore |   | Tutti |   |
| Categoria del contatore | lineItem/ProductCode | Tutti |   |
| Sottocategoria di contatore | lineitem/UsageType | Tutti |   |
| Operazione | lineItem/Operation | Tutti |   |
| Risorsa | lineItem/ResourceId | Tutti |   |
| Tipo di risorsa | product/instanceType | Tutti | Se product/instanceType è Null, viene usato lineItem/UsageType. |
| ResourceGuid | N/D | Tutti | GUID del contatore di Azure. |
| Nome servizio | product/ProductName | Tutti | Se product/ProductName è Null, viene usato lineItem/ProductCode. |
| Livello di servizio |   |   |   |
| ID sottoscrizione | lineItem/UsageAccountId | Account consolidato e gruppo di gestione |   |
| Nome sottoscrizione | N/D | Account consolidato e gruppo di gestione | I nomi degli account vengono raccolti usando l'API AWS Organization. |
| Tag | resourceTags/\* | Tutti | Il prefisso _user:_ viene rimosso dai tag definiti dall'utente per consentire i tag tra cloud. Il prefisso _aws:_ rimane intatto. |
| ID account di fatturazione | bill/PayerAccountId | Gruppo di gestione |   |
| Nome dell'account di fatturazione | N/D | Gruppo di gestione | I nomi degli account vengono raccolti usando l'API AWS Organization. |
| Provider | N/D | Gruppo di gestione | AWS o Azure. |

## <a name="set-budgets-on-aws-scopes"></a>Impostare budget negli ambiti di AWS

Usare i budget per gestire in modo proattivo i costi e promuovere la responsabilità nell'organizzazione. I budget vengono impostati negli ambiti dell'account AWS consolidato e dell'account AWS collegato. Di seguito è riportato un esempio di budget per un account AWS consolidato visualizzati in Gestione costi:

![Esempio che illustra i budget per un account AWS consolidato](./media/aws-integration-manage/budgets-aws-consolidated-account01.png)

## <a name="aws-data-collection-process"></a>Processo di raccolta dati di AWS

Dopo la configurazione del connettore AWS iniziano i processi di raccolta e individuazione dati. La raccolta di tutti i dati di utilizzo può richiedere alcune ore. La durata dipende da:

- Il tempo necessario per elaborare i file del report di costi e utilizzo presenti nel bucket di AWS S3.
- Il tempo necessario per creare gli ambiti dell'account AWS consolidato e dell'account AWS collegato.
- Il tempo e la frequenza con cui AWS scrive i file del report di costi e utilizzo nel bucket S3

## <a name="aws-integration-pricing"></a>Prezzi dell'integrazione di AWS

Per ogni connettore AWS viene concessa una prova gratuita di 90 giorni. Durante l'anteprima pubblica non è previsto alcun addebito.

Il prezzo di listino è l'1% dei costi mensili di AWS. Ogni mese viene addebitato un importo in base ai costi fatturati del mese precedente.

L'accesso alle API di AWS può comportare costi aggiuntivi.

## <a name="aws-integration-limitations"></a>Limitazioni dell'integrazione di AWS

- Gestione costi non supporta i report di costi che contengono più tipi di valuta. Se si seleziona un ambito con più valute, viene visualizzato un messaggio di errore.
- I connettori cloud non supportano AWS GovCloud (US), AWS Gov o AWS Cina.
- Gestione costi visualizza solo i _costi di utilizzo_ di AWS. Imposte, supporto, rimborsi, istanze riservate, crediti o altri tipi di addebito non sono ancora supportati.

## <a name="troubleshooting-aws-integration"></a>Risoluzione dei problemi di integrazione di AWS

Per risolvere problemi comuni usare le informazioni riportate di seguito.

### <a name="no-permission-to-aws-linked-accounts"></a>Nessuna autorizzazione per gli account AWS collegati

**Codice errore:** _Non autorizzato_

Esistono due modi per ottenere le autorizzazioni per accedere ai costi degli account AWS collegati:

- Ottenere l'accesso al gruppo di gestione contenente gli account AWS collegati.
- Richiedere l'autorizzazione necessaria per l'account AWS collegato.

Per impostazione predefinita, il creatore del connettore AWS è il proprietario di tutti gli oggetti creati dal connettore. Questi oggetti includono l'account AWS consolidato e l'account AWS collegato.

Per poter verificare le impostazioni del connettore è necessario avere almeno un ruolo Collaboratore. Il lettore non può verificare le impostazioni del connettore.

### <a name="collection-failed-with-assumerole"></a>Raccolta non riuscita con AssumeRole

**Codice errore:** _FailedToAssumeRole_

Questo errore indica che Gestione costi non riesce a chiamare l'API AssumeRole di AWS. Può verificarsi a causa di un problema nella definizione del ruolo. Verificare che le condizioni seguenti siano soddisfatte:

- L'ID esterno corrisponde a quello nella definizione del ruolo e nella definizione del connettore.
- Il tipo di ruolo è impostato su **Another AWS account Belonging to you or 3rd party** (Un altro account AWS appartenente all'utente o a una terza parte).
- L'opzione **Require MFA** (Richiedi MFA) è deselezionata.
- L'account AWS attendibile nel ruolo AWS è _432263259397_.

### <a name="collection-failed-with-access-denied---cur-report-definitions"></a>Raccolta non riuscita con accesso negato - Definizioni del report di costi e utilizzo

**Codice errore:** _AccessDeniedReportDefinitions_

Questo errore indica che Gestione costi non riesce a vedere le definizioni del report di costi e utilizzo. Questa autorizzazione viene usata per verificare che il report di costi e utilizzo sia definito come previsto da Gestione costi di Azure. Vedere [Creare un report di costi e utilizzo in AWS](aws-integration-set-up-configure.md#create-a-cost-and-usage-report-in-aws).

### <a name="collection-failed-with-access-denied---list-reports"></a>Raccolta non riuscita con accesso negato - Elenca report

**Codice errore:** _AccessDeniedListReports_

Questo errore indica che Gestione costi non riesce a elencare gli oggetti nel bucket S3 in cui si trova il report di costi e utilizzo. Il criterio IAM di AWS richiede un'autorizzazione sul bucket e sugli oggetti presenti nel bucket. Vedere [Creare un ruolo e un criterio in AWS](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws).

### <a name="collection-failed-with-access-denied---download-report"></a>Raccolta non riuscita con accesso negato - Scarica report

**Codice errore:** _AccessDeniedDownloadReport_

Questo errore indica che Gestione costi non riesce ad accedere e a scaricare i file del report di costi e utilizzo archiviati nel bucket di Amazon S3. Verificare che il criterio JSON di AWS associato al ruolo sia simile all'esempio illustrato alla fine della sezione [Creare un ruolo e un criterio in AWS](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws).

### <a name="collection-failed-since-we-did-not-find-the-cost-and-usage-report"></a>Raccolta non riuscita poiché il report di costi e utilizzo non è stato trovato

**Codice errore:** _FailedToFindReport_

Questo errore indica che Gestione costi non riesce a trovare il report di costi e utilizzo definito nel connettore. Verificare che non sia stato eliminato e che il criterio JSON di AWS associato al ruolo sia simile all'esempio illustrato alla fine della sezione [Creare un ruolo e un criterio in AWS](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws).

### <a name="unable-to-create-or-verify-connector-due-to-cost-and-usage-report-definitions-mismatch"></a>Impossibile creare o verificare il connettore a causa delle definizioni del report di costi e utilizzo non corrispondenti

**Codice errore:** _ReportIsNotValid_

Questo errore è relativo alla definizione del report di costi e utilizzo di AWS. Per questo report sono necessarie impostazioni specifiche. Vedere i requisiti in [Creare un report di costi e utilizzo in AWS](aws-integration-set-up-configure.md#create-a-cost-and-usage-report-in-aws).

## <a name="next-steps"></a>Passaggi successivi

- Se l'ambiente di Azure non è stato ancora configurato con i gruppi di gestione, vedere [Configurazione iniziale dei gruppi di gestione](../../governance/management-groups/overview.md#initial-setup-of-management-groups).
