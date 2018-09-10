---
title: Monitorare un test di convalida di Azure Stack come servizio | Microsoft Docs
description: Monitorare un test di convalida di Azure Stack come servizio.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 2dc4d3f2855864ff80648b5b9635ff28c0dacbb7
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/07/2018
ms.locfileid: "44163330"
---
# <a name="monitor-a-test-with-azure-stack-validation-as-a-service"></a>Monitorare un test di convalida di Azure Stack come servizio

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

L'esecuzione di un test può essere monitorato visualizzando la **operazioni** pagina per i gruppi di test che sono in corso o completata. Questa pagina illustra in dettaglio lo stato del test e le relative operazioni.

## <a name="monitor-a-test"></a>Monitorare un test

1. Selezionare una soluzione.

2. Selezionare **Gestisci** su qualsiasi riquadro del flusso di lavoro.

3. Fare clic su un flusso di lavoro per aprire la pagina di riepilogo del test.

4. Espandere il menu di scelta rapida **[...]**  per qualsiasi istanza di suite di test.

5. Selezionare **visualizzare operazioni**

![Alt text](media\image4.png)

Per i test che hanno terminato l'esecuzione, i log possono essere scaricati dalla pagina di riepilogo test facendo clic su **scaricare i log** nel menu di scelta rapida di un test **[...]** . Partner di Azure Stack è possibile usare questi log per il debug dei problemi per i test non superati.

## <a name="open-the-test-pass-summary"></a>Aprire il riepilogo del passaggio di test

1. Aprire il portale. 
2. Selezionare il nome di una soluzione esistente che in precedenza contiene test eseguito o pianificato.

    ![Gestire i test superati](media/managetestpasses.png)

3. Selezionare **Manage** nel **Test superati** pannello.
4. Selezionare il passaggio di test per aprire il riepilogo del passaggio di Test. È possibile esaminare il nome del test, data di creazione, esecuzione, il tempo impiegato il test e il risultato (esito positivo o negativo).
5. Selezionare [ **.  .** ].

### <a name="test-pass-summary"></a>Riepilogo del test di pass

| Colonna | DESCRIZIONE |
| --- | --- |
| Nome test | Il nome del test. Fa riferimento al numero di convalida. |
| Data di creazione | Ora che è stato creato il superamento del test. |
| Started | Ora che è stato eseguito il test precedente. |
| Duration | Periodo di tempo il tempo impiegato per eseguire il passaggio di test. |
| Status | Il risultato (Succeeded o Failed) per il passaggio di rest. |
| Nome agente | Il nome di dominio completo dell'agente. |
| Operazioni totali | Numero totale di operazioni ha tentato nella sessione di test. |
| Operazioni passate | Il numero di operazioni che passato il superamento del test. |
|  Operazioni non riuscite | Il numero di operazioni riuscite. |

### <a name="group-columns-in-the-test-pass-summary"></a>Colonne di gruppo nel test di passare riepilogo

È possibile selezionare e trascinare una colonna nell'intestazione per creare un gruppo nel valore della colonna.

## <a name="reschedule-a-test"></a>Modificare la pianificazione di un test

1. [Aprire il riepilogo del test pass](#open-the-test-pass-summary).
2. Selezionare **ripianificare** ripianificare il superamento del test.
3. Immettere la password dell'amministratore di Cloud per l'istanza di Azure Stack.
4. Immettere la stringa di connessione di archiviazione di diagnostica che è definito quando si configura l'account.
5. Modificare la pianificazione di test.

## <a name="cancel-a-test"></a>Annulla un test

1. [Aprire il riepilogo del test pass](#open-the-test-pass-summary).
2. Selezionare **Annulla**.

## <a name="get-test-information"></a>Ottenere le informazioni sul test

1. [Aprire il riepilogo del test pass](#open-the-test-pass-summary).
2. Selezionare **consente di visualizzare informazioni** ripianificare il superamento del test.

**Informazioni sul test**

| NOME | DESCRIZIONE |
| -- | -- |
| Nome test | Il nome del test, ad esempio, OEM aggiornamento in Azure Stack 1806 RC convalida. |
| Versione di prova | La versione del test, ad esempio, 5.1.4.0. |
| Editore | Editore di test, ad esempio Microsoft. |
| Categoria | La categoria del test, ad esempio **funzionale** oppure **affidabilità**. |
| Servizi di destinazione | I servizi sottoposto a test, ad esempio VirtualMachines |
| DESCRIZIONE | La descrizione del test. |
| Durata stimata (minuti) | Il periodo di tempo in minuti per l'esecuzione ha richiesto il test. |
| Collegamenti | Collegamento a GitHub Issue Tracker. |

## <a name="get-test-parameters"></a>Ottenere i parametri di test

1. [Aprire il riepilogo del test pass](#open-the-test-pass-summary).
2. Selezionare **visualizzare i parametri** ripianificare il superamento del test.

**Parameters**

| NOME | DESCRIZIONE |
| -- | -- |
| Nome test | Il nome del test, ad esempio, oemupdate1806test. |
| Versione di prova | La versione del resto, ad esempio, 5.1.4.0. |
| ID dell'istanza di test | Un GUID che identifica l'istanza specifica del test, ad esempio, 20b20645-b400-4f0d-bf6f-1264d866ada9. |
| cloudAdminUser | Il nome dell'account usato come amministratore del cloud, ad esempio, **cloudadmin**. |
| DiagnosticsContainerName | L'ID del contenitore di diagnostica, ad esempio, 04dd3815-5f35-4158-92ea-698027693080. |

## <a name="get-test-operations"></a>Ottiene le operazioni di test

1. [Aprire il riepilogo del test pass](#open-the-test-pass-summary).
2. Selezionare **per visualizzare le operazioni** ripianificare il superamento del test. Viene visualizzato il riquadro di riepilogo di operazioni.

## <a name="get-test-logs"></a>Ottenere i log dei test

1. [Aprire il riepilogo del test pass](#open-the-test-pass-summary).
2. Selezionare **scaricare i log** ripianificare il superamento del test.  
    Un file zip denominato ReleaseYYYY-MM-DD.zip che contiene i download dei log.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sulle [convalida Azure Stack come servizio](https://docs.microsoft.com/azure/azure-stack/partner).
