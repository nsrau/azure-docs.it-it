---
title: Installare l'aggiornamento nel dispositivo della serie Azure Data Box Gateway | Microsoft Docs
description: Viene descritto come applicare gli aggiornamenti usando il portale di Azure e l'interfaccia utente Web locale per il dispositivo della serie Azure Data Box Gateway
services: databox
author: alkohli
ms.service: databox
ms.topic: article
ms.date: 06/30/2020
ms.author: alkohli
ms.openlocfilehash: 1b3f0faa2b5f67a23317935f0ad868e3872cf86e
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/13/2020
ms.locfileid: "90055964"
---
# <a name="update-your-azure-data-box-gateway"></a>Aggiornare la Azure Data Box Gateway

Questo articolo descrive i passaggi necessari per installare l'aggiornamento nella Azure Data Box Gateway tramite l'interfaccia utente Web locale e la portale di Azure. Applicare gli aggiornamenti software o gli hotfix per rendere aggiornato il dispositivo Data Box Gateway.

> [!IMPORTANT]
>
> - L'aggiornamento **1911** corrisponde alla versione del software **1.6.1049.786** nel dispositivo. Per informazioni su questo aggiornamento, vedere [Note sulla versione](data-box-gateway-1911-release-notes.md).
>
> - Tenere presente che l'installazione di un aggiornamento o un hotfix potrebbe riavviare il dispositivo. Poiché il Data Box Gateway è un dispositivo a nodo singolo, eventuali operazioni di I/O in corso verranno interrotte e il dispositivo rimarrà inattivo per un tempo massimo di 30 minuti per consentire l'aggiornamento del software.

Ogni passaggio viene descritto nelle sezioni seguenti.

## <a name="use-the-azure-portal"></a>Usare il portale di Azure

Si consiglia di installare gli aggiornamenti tramite il portale di Azure. Il dispositivo analizza automaticamente gli aggiornamenti una volta al giorno. Quando gli aggiornamenti sono disponibili, viene visualizzata una notifica nel portale. È quindi possibile scaricare e installare gli aggiornamenti.

> [!NOTE]
> Verificare che il dispositivo sia integro e che lo stato venga visualizzato come **online** prima di procedere con l'installazione degli aggiornamenti.

1. Quando gli aggiornamenti sono disponibili per il dispositivo, viene visualizzata una notifica. Selezionare la notifica o la barra dei comandi superiore, **Aggiorna dispositivo**. Questo consentirà di applicare gli aggiornamenti software del dispositivo.

    ![Versione del software dopo l'aggiornamento](./media/data-box-gateway-apply-updates/portal-apply-update-01a.png)

2. Nel pannello **aggiornamenti del dispositivo** verificare di aver esaminato le condizioni di licenza associate alle nuove funzionalità nelle note sulla versione.

    È possibile scegliere di **scaricare e installare** gli aggiornamenti o semplicemente **scaricare** gli aggiornamenti. Sarà così possibile installarli in un secondo momento.

    ![Versione del software dopo l'aggiornamento](./media/data-box-gateway-apply-updates/portal-apply-update-02.png)

    Se si desidera scaricare e installare gli aggiornamenti, selezionare l'opzione installa automaticamente aggiornamenti al termine del download.

    ![Versione del software dopo l'aggiornamento](./media/data-box-gateway-apply-updates/portal-apply-update-03.png)

3. Viene avviato il download degli aggiornamenti. Viene visualizzata una notifica che indica che il download è in corso.

    ![Versione del software dopo l'aggiornamento](./media/data-box-gateway-apply-updates/portal-apply-update-05.png)

    Nel portale di Azure viene visualizzato anche un banner di notifica. Indica lo stato del download.

    ![Versione del software dopo l'aggiornamento](./media/data-box-gateway-apply-updates/portal-apply-update-08a.png)

    È possibile selezionare questa notifica oppure selezionare **Aggiorna dispositivo** per visualizzare lo stato dettagliato dell'aggiornamento.

    ![Versione del software dopo l'aggiornamento](./media/data-box-gateway-apply-updates/portal-apply-update-09.png)

4. Al termine del download, il banner di notifica viene aggiornato per indicare il completamento. Se si sceglie di scaricare e installare gli aggiornamenti, l'installazione verrà avviata automaticamente.

    ![Versione del software dopo l'aggiornamento](./media/data-box-gateway-apply-updates/portal-apply-update-10a.png)

    Se si sceglie di scaricare solo gli aggiornamenti, selezionare la notifica per aprire il pannello **aggiornamenti del dispositivo** . Selezionare **Installa**.
  
    ![Versione del software dopo l'aggiornamento](./media/data-box-gateway-apply-updates/portal-apply-update-11a.png)

5. Viene visualizzata una notifica che indica che l'installazione è in corso.

    ![Versione del software dopo l'aggiornamento](./media/data-box-gateway-apply-updates/portal-apply-update-12a.png)

    Il portale Visualizza inoltre un avviso informativo per indicare che l'installazione è in corso. <!-- The device goes offline and is in maintenance mode.-->

    <!-- ![Software version after update](./media/data-box-gateway-apply-updates/update-13.png)-->

6. Poiché si tratta di un dispositivo a 1 nodo, il dispositivo verrà riavviato dopo l'installazione degli aggiornamenti. L'avviso critico durante il riavvio indicherà che l'heartbeat del dispositivo è andato perso.

    ![Versione del software dopo l'aggiornamento](./media/data-box-gateway-apply-updates/portal-apply-update-19a.png)

    Selezionare l'avviso per visualizzare l'evento del dispositivo corrispondente.

    ![Versione del software dopo l'aggiornamento](./media/data-box-gateway-apply-updates/portal-apply-update-20a.png)

7. Lo stato del dispositivo viene aggiornato a **online** dopo l'installazione degli aggiornamenti.

    ![Versione del software dopo l'aggiornamento](./media/data-box-gateway-apply-updates/portal-apply-update-23a.png)

    Dalla barra dei comandi superiore selezionare **aggiornamenti del dispositivo**. Verificare che l'aggiornamento sia stato installato correttamente e che la versione del software del dispositivo lo rispecchi.

    ![Versione del software dopo l'aggiornamento](./media/data-box-gateway-apply-updates/portal-apply-update-24.png)

## <a name="use-the-local-web-ui"></a>Usare l'interfaccia utente Web locale

Quando si usa l'interfaccia utente Web locale, è necessario eseguire due passaggi:

- Scaricare l'aggiornamento o l'hotfix
- Installare l'aggiornamento o l'hotfix

Ogni passaggio viene descritto dettagliatamente nelle sezioni seguenti.

### <a name="download-the-update-or-the-hotfix"></a>Scaricare l'aggiornamento o l'hotfix

Per scaricare l'aggiornamento, seguire questa procedura. È possibile scaricare l'aggiornamento dal percorso fornito da Microsoft o dal catalogo Microsoft Update.

Eseguire i passaggi seguenti per scaricare l'aggiornamento dal catalogo Microsoft Update.

1. Avviare il browser e passare a [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com) .

   ![Cercare nel catalogo](./media/data-box-gateway-apply-updates/download-update-1.png)

2. Nella casella di ricerca del catalogo Microsoft Update, immettere il numero della Knowledge base (KB) dell'hotfix o le condizioni per l'aggiornamento che si desidera scaricare. Ad esempio, immettere **Azure Data Box gateway**e quindi fare clic su **Cerca**.

   L'elenco di aggiornamenti viene visualizzato come **Azure Data Box Gateway 1911**.

   ![Cercare nel catalogo](./media/data-box-gateway-apply-updates/download-update-2.png)

3. Selezionare **Download**. È disponibile un singolo file da scaricare denominato *SoftwareUpdatePackage.exe* corrispondente all'aggiornamento del software del dispositivo. Scaricare il file in una cartella nel sistema locale. Inoltre, la cartella può essere copiata in una condivisione di rete raggiungibile dal dispositivo.

   ![Cercare nel catalogo](./media/data-box-gateway-apply-updates/download-update-3.png)

### <a name="install-the-update-or-the-hotfix"></a>Installare l'aggiornamento o l'hotfix

Prima dell'installazione di un aggiornamento o un hotfix, assicurarsi:

- Di avere scaricato l'aggiornamento o l'hotfix in locale o sull'host, altrimenti che siano accessibili tramite una condivisione di rete.
- Lo stato del dispositivo è integro, come illustrato nella pagina **Panoramica** dell'interfaccia utente Web locale.

   ![aggiornamento dispositivo](./media/data-box-gateway-apply-updates/local-ui-update-1.png)

Il completamento di questa procedura richiede circa 20 minuti. Seguire questa procedura per installare l'aggiornamento o l'hotfix.

1. Nell'interfaccia utente Web locale passare a **manutenzione**  >  **aggiornamento software**. Prendere nota della versione del software in esecuzione.

   ![aggiornamento dispositivo](./media/data-box-gateway-apply-updates/local-ui-update-2.png)

2. Consente di specificare il percorso del file di aggiornamento. È anche possibile passare al file di installazione dell'aggiornamento, se inserito in una condivisione di rete. Selezionare il file di aggiornamento software con il suffisso *SoftwareUpdatePackage.exe* .

   ![aggiornamento dispositivo](./media/data-box-gateway-apply-updates/local-ui-update-3.png)

3. Selezionare **Applica**.

   ![aggiornamento dispositivo](./media/data-box-gateway-apply-updates/local-ui-update-4.png)

4. Quando viene richiesta la conferma, selezionare **Sì** per continuare. Dato che il dispositivo è un dispositivo a nodo singolo, dopo l'applicazione dell'aggiornamento il dispositivo viene riavviato e si verifica un tempo di inattività.
   ![aggiornare il dispositivo](./media/data-box-gateway-apply-updates/local-ui-update-5.png)

5. L'aggiornamento si avvia. Dopo l'aggiornamento il dispositivo si riavvia in automatico. In questo periodo di tempo l'interfaccia utente locale non è accessibile.

6. Al termine del riavvio si viene indirizzati alla pagina **di accesso** . Per verificare che il software del dispositivo sia stato aggiornato, nell'interfaccia utente Web locale passare a **manutenzione**  >  **aggiornamento software**. La versione del software visualizzata in questo esempio è **1.6.1049.786**.

   ![aggiornamento dispositivo](./media/data-box-gateway-apply-updates/local-ui-update-6.png)

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sull' [amministrazione del Azure Data Box gateway](data-box-gateway-manage-users.md).
