---
title: Registrare il servizio di peering di Azure-portale di Azure
description: Informazioni su come registrare il servizio peering di Azure usando il portale di Azure
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 62210105ba4ba84cff4b246c5b7cf8cc794fb5e7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91534948"
---
# <a name="register-peering-service-by-using-the-azure-portal"></a>Registrare il servizio di peering usando il portale di Azure

Il servizio di peering di Azure è un servizio di rete che migliora la connettività dei clienti ai servizi cloud Microsoft, come Microsoft 365, Dynamics 365, i servizi SaaS (Software as a Service), Azure o qualsiasi servizio Microsoft accessibile tramite la rete Internet pubblica.

Questo articolo illustra come registrare una connessione al servizio di peering usando il portale di Azure.

Se non si ha una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) ora.

> 

## <a name="prerequisites"></a>Prerequisiti

È necessario disporre di quanto segue:

### <a name="azure-account"></a>Account Azure

Occorre avere un account Microsoft Azure valido e attivo. Questo account è necessario per configurare la connessione al servizio di peering. Il servizio di peering è una risorsa all'interno delle sottoscrizioni di Azure. 

### <a name="connectivity-provider"></a>Provider di connettività

È possibile collaborare con un provider di servizi Internet o Internet Exchange per ottenere il servizio di peering in modo da connettere la propria rete alla rete Microsoft.

Assicurarsi che i [provider di connettività](location-partners.md) collaborino con Microsoft.



## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Da un browser passare alla portale di Azure e accedere con l'account Azure.

## <a name="register-a-peering-service-connection"></a>Registrare una connessione al Servizio di peering

1. Per registrare una connessione al servizio di peering, selezionare **Crea una risorsa** > **Servizio di peering**.

    ![Registrare il Servizio di peering](./media/peering-service-portal/peering-servicecreate.png)
1. Immettere i dettagli seguenti nella scheda **Informazioni di base** della pagina **Create a peering service connection** (Crea una connessione al servizio di peering).

 
1. Selezionare la sottoscrizione e il gruppo di risorse associato alla sottoscrizione.

   ![Scheda di base per la registrazione del peering](./media/peering-service-portal/peering-servicebasics.png)

1. In **Nome** immettere un nome con cui registrare l'istanza del servizio di peering.
 
1. Selezionare quindi il pulsante **Avanti: Configurazione** nella parte inferiore della pagina. Viene visualizzata la pagina **Configurazione**.

## <a name="configure-the-peering-service-connection"></a>Configurare la connessione al servizio di peering

1. Nella pagina **Configurazione** selezionare la località in cui deve essere abilitato il servizio di peering dall'elenco a discesa **Peering service location** (Località servizio di peering).

1. Selezionare il provider di servizi da cui deve essere ottenuto il servizio di peering selezionando un nome di provider nell'elenco a discesa **Peering service provider** (Provider di servizi di peering).
 
1. Selezionare **Create new prefix** (Crea nuovo prefisso) nella parte inferiore della sezione **Prefissi**. Verranno visualizzate delle caselle di testo. Immettere il nome della risorsa prefisso e i prefissi associati al provider di servizi.

1. Selezionare **Prefix Key** (Chiave prefisso) e aggiungere la chiave di prefisso fornita dal provider (ISP o IXP). Questa chiave consente a Microsoft di convalidare il prefisso e il provider che ha allocato il prefisso IP.
   > ![Screenshot che mostra la scheda Configurazione della pagina per la creazione di una connessione al servizio di peering in cui è possibile immettere la chiave del prefisso.](./media/peering-service-portal/peering-serviceconfiguration.png)

1. Selezionare il pulsante **Rivedi e crea** nell'angolo inferiore sinistro della pagina. Viene visualizzata la pagina **Rivedi e crea** e Azure convalida la configurazione.
    

1. Quando viene visualizzato il messaggio **Convalida superata**, come illustrato di seguito, selezionare **Crea**.

   > ![Screenshot che mostra la scheda Rivedi e crea della pagina per la creazione di una connessione al servizio di peering.](./media/peering-service-portal/peering-service-prefix.png)


1. Dopo la registrazione di una connessione al servizio di peering, viene eseguita un'ulteriore convalida sui prefissi inclusi. È possibile esaminare lo stato della convalida nella sezione **Prefissi** del nome della risorsa. Se la convalida non riesce, viene visualizzato un messaggio simile a uno dei messaggi di errore seguenti:

   - Il prefisso del servizio di peering non è valido. Il prefisso deve essere in un formato valido. È supportato solo il prefisso IPv4.
   - Il prefisso non è stato ricevuto dal provider di servizi di peering.
   - L'annuncio di prefisso non dispone di una community BGP valida, contattare il provider del servizio di peering.
   - Impossibile trovare la route di backup, contattare il provider del servizio di peering.
   - Prefisso ricevuto con il nome di percorso più lungo, contattare il provider del servizio di peering.
   - Prefisso ricevuto con privato come nel percorso, contattare il provider del servizio di peering.

### <a name="add-or-remove-a-prefix"></a>Aggiungere o rimuovere un prefisso

Per aggiungere prefissi, selezionare **Aggiungi prefissi** nella pagina **Prefissi**.

Selezionare i puntini di sospensione (...) accanto al prefisso elencato e quindi selezionare l'opzione **Elimina**.

### <a name="delete-a-peering-service-connection"></a>Eliminare una connessione al servizio di peering

Nella pagina **Tutte le risorse** selezionare la casella di controllo accanto al servizio di peering e quindi selezionare l'opzione **Elimina** nella parte superiore della pagina.

> [!NOTE]
> Non è possibile modificare un prefisso esistente.
>

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sulla connessione al servizio di peering, vedere [Connessione al servizio di peering](connection.md).
- Per informazioni sui dati di telemetria delle connessioni al servizio di peering, vedere [Telemetria delle connessioni al servizio di peering](connection-telemetry.md).
- Per misurare i dati di telemetria, vedere [Misurare la telemetria delle connessioni](measure-connection-telemetry.md).
- Per registrare la connessione usando Azure PowerShell, vedere [Registrare una connessione al servizio di peering - Azure PowerShell](powershell.md).
- Per registrare la connessione usando l'interfaccia della riga di comando di Azure, vedere [Registrare una connessione al servizio di peering - Interfaccia della riga di comando di Azure](cli.md).
