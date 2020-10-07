---
title: 'Servizio di peering di Azure: Creazione '
description: In questa esercitazione viene illustrato come registrare il servizio di peering di Azure e un prefisso.
services: peering-service
author: derekolo
ms.service: peering-service
ms.topic: tutorial
ms.date: 05/2/2020
ms.author: derekol
Customer intent: With Azure Peering service enhancing the customer connectivity to Microsoft cloud services .
ms.openlocfilehash: f8f0ee71a8dd00498d868bb6dc11b3fa083df766
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91530219"
---
# <a name="tutorial-create-a-peering-service-connection"></a>Esercitazione: Creare una connessione al Servizio di peering

Questa esercitazione illustra come creare una risorsa del servizio di peering e come configurare una connessione al servizio di peering. 

1. Per registrare una connessione al servizio di peering, selezionare **Crea una risorsa** > **Servizio di peering**.

 
    ![Registrare il Servizio di peering](./media/peering-service-portal/peering-servicecreate.png)

2. Immettere i dettagli seguenti nella scheda **Informazioni di base** della pagina **Create a peering service connection** (Crea una connessione al servizio di peering).
 
3. Selezionare la sottoscrizione e il gruppo di risorse associato alla sottoscrizione.

    ![Scheda Informazioni di base per la registrazione del servizio di peering](./media/peering-service-portal/peering-servicebasics.png)

4. In **Nome** immettere un nome con cui registrare l'istanza del servizio di peering.

5. Selezionare quindi il pulsante **Avanti: Configurazione** nella parte inferiore della pagina. Viene visualizzata la pagina **Configurazione**.
## <a name="configure-the-peering-service-connection"></a>Configurare la connessione al servizio di peering

1. Nella pagina **Configurazione** selezionare la località in cui deve essere abilitato il servizio di peering dall'elenco a discesa **Peering service location** (Località servizio di peering).

1. Selezionare il provider di servizi da cui deve essere ottenuto il servizio di peering selezionando un nome di provider nell'elenco a discesa **Peering service provider** (Provider di servizi di peering).
 
1. Selezionare **Create new prefix** (Crea nuovo prefisso) nella parte inferiore della sezione **Prefissi**. Verranno visualizzate delle caselle di testo. Immettere il nome della risorsa prefisso e i prefissi associati al provider di servizi.

1. Selezionare **Prefix Key** (Chiave prefisso) e aggiungere la chiave di prefisso fornita dal provider (ISP o IXP). Questa chiave consente a Microsoft di convalidare il prefisso e il provider che ha allocato il prefisso IP.

    ![Screenshot che mostra la scheda Configurazione della pagina per la creazione di una connessione al servizio di peering in cui è possibile immettere la chiave del prefisso.](./media/peering-service-portal/peering-serviceconfiguration.png)

1. Selezionare il pulsante **Rivedi e crea** nell'angolo inferiore sinistro della pagina. Viene visualizzata la pagina **Rivedi e crea** e Azure convalida la configurazione.

 1. Quando viene visualizzato il messaggio **Convalida superata**, come illustrato di seguito, selezionare **Crea**.

> ![Screenshot che mostra la scheda Rivedi e crea della pagina per la creazione di una connessione al servizio di peering.](./media/peering-service-portal/peering-service-prefix.png)

1. Dopo la registrazione di una connessione al servizio di peering, viene eseguita un'ulteriore convalida sui prefissi inclusi. È possibile esaminare lo stato della convalida nella sezione **Prefissi** del nome della risorsa. Se la convalida non riesce, viene visualizzato un messaggio simile a uno dei messaggi di errore seguenti:

   - Il prefisso del servizio di peering non è valido. Il prefisso deve essere in un formato valido. È supportato solo il prefisso IPv4.
   - Il prefisso non è stato ricevuto dal provider di servizi di peering.
   - L'annuncio del prefisso non ha una community BGP valida. Contattare il provider del servizio di peering.
   - Route di backup non trovata. Contattare il provider del servizio di peering.
   - Prefisso ricevuto con un percorso AS più lungo. Contattare il provider del servizio di peering.
   - Prefisso ricevuto con AS privato nel percorso. Contattare il provider del servizio di peering.

### <a name="add-or-remove-a-prefix"></a>Aggiungere o rimuovere un prefisso

Per aggiungere prefissi, selezionare **Aggiungi prefissi** nella pagina **Prefissi**.

Selezionare i puntini di sospensione (...) accanto al prefisso elencato e quindi selezionare l'opzione **Elimina**.

### <a name="delete-a-peering-service-connection"></a>Eliminare una connessione al servizio di peering

Nella pagina **Tutte le risorse** selezionare la casella di controllo accanto al servizio di peering e quindi selezionare l'opzione **Elimina** nella parte superiore della pagina.
## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sulla connessione al servizio di peering, vedere [Connessione al servizio di peering](connection.md).
- Per informazioni sui dati di telemetria delle connessioni al servizio di peering, vedere [Telemetria delle connessioni al servizio di peering](connection-telemetry.md).
- Per misurare i dati di telemetria, vedere [Misurare la telemetria delle connessioni](measure-connection-telemetry.md).
- Per registrare la connessione usando Azure PowerShell, vedere [Registrare una connessione al servizio di peering - Azure PowerShell](powershell.md).
- Per registrare la connessione usando l'interfaccia della riga di comando di Azure, vedere [Registrare una connessione al servizio di peering - Interfaccia della riga di comando di Azure](cli.md).