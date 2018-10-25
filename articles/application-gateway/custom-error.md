---
title: Creare pagine di errore personalizzate del gateway applicazione di Azure
description: Questo articolo illustra come creare pagine di errore personalizzate del gateway applicazione.
services: application-gateway
author: amitsriva
ms.service: application-gateway
ms.topic: article
ms.date: 10/11/2018
ms.author: victorh
ms.openlocfilehash: 2f76347105743538e9fc1d7588ecb949f2675696
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2018
ms.locfileid: "49071029"
---
# <a name="create-application-gateway-custom-error-pages"></a>Creare pagine di errore personalizzate del gateway applicazione

Il gateway applicazione consente di creare pagine di errore personalizzate da visualizzare al posto delle pagine di errore predefinite. Se si usa una pagina di errore personalizzata, è possibile usare il proprio layout e marchio aziendali.

È possibile, ad esempio, definire la propria pagina di manutenzione se l'applicazione Web non è raggiungibile. In alternativa, è possibile creare una pagina di accesso non autorizzato se viene inviata una richiesta dannosa a un'applicazione Web.

Le pagine di errore personalizzate sono supportate per i due scenari seguenti:

- **Pagina Manutenzione**: questa pagina di errore personalizzata viene inviata al posto di una pagina 502 - Gateway non valido. Viene visualizzata quando il gateway applicazione non dispone di back-end a cui indirizzare il traffico. Quando, ad esempio, è pianificata la manutenzione oppure quando un problema imprevisto influisce sull'accesso al pool back-end.
- **Pagina Accesso non autorizzato**: questa pagina di errore personalizzata viene inviata al posto di una pagina 403 - Accesso non autorizzato. Viene visualizzata quando il WAF del gateway applicazione rileva traffico dannoso e lo blocca.

Se un errore proviene dai server back-end, viene reinviato senza modifiche al chiamante. Non viene visualizzata una pagina di errore personalizzata. Il gateway applicazione può visualizzare una pagina di errore personalizzata quando una richiesta non riesce a raggiungere il back-end.

Le pagine di errore personalizzate possono essere definite a livello globale e a livello di listener:

- **Livello globale**: la pagina di errore è applicabile al traffico di tutte le applicazioni Web distribuite nel gateway applicazione specifico.
- **Livello di listener**: la pagina di errore è applicabile al traffico ricevuto nel listener specifico.
- **Entrambi i livelli**: la pagina di errore personalizzata definita a livello di listener sostituisce quella impostata a livello globale.

Per creare una pagina di errore personalizzata è necessario disporre di:
- un codice di stato risposta HTTP;
- il percorso corrispondente per la pagina di errore; 
- un BLOB del servizio di archiviazione di Azure accessibile pubblicamente per il percorso;
- un tipo di estensione *.htm o *.HTML. 

Le dimensioni della pagina di errore devono essere minori di 1 MB. Se nella pagina di errore sono presenti immagini collegate, queste devono essere un URL assoluto accessibile pubblicamente o un'immagine con codifica Base64 inline nella pagina di errore personalizzata. I collegamenti relativi con immagini nello stesso percorso BLOB non sono attualmente supportati. 

Dopo che l'utente ha specificato una pagina di errore, il gateway applicazione la scarica dal percorso BLOB del servizio di archiviazione e la salva nella cache locale del gateway applicazione. La pagina di errore viene quindi distribuita direttamente dal gateway applicazione. Per modificare una pagina di errore personalizzata esistente, è necessario puntare a un percorso BLOB diverso nella configurazione del gateway applicazione. Il gateway applicazione non verifica periodicamente il percorso BLOB per recuperare le nuove versioni.

## <a name="portal-configuration"></a>Configurazione del portale

1. Passare a Gateway applicazione nel portale e scegliere un gateway applicazione.

    ![Panoramica dei gruppi di disponibilità](media/custom-error/ag-overview.png)
2. Fare clic su **Listener** e passare a un listener specifico in cui si desidera specificare una pagina di errore.

    ![Listener dei gateway applicazione](media/custom-error/ag-listener.png)
3. Configurare una pagina di errore personalizzata per un errore WAF 403 o una pagina di manutenzione 502 a livello di listener.

    > [!NOTE]
    > La creazione di pagine di errore personalizzate a livello globale dal portale di Azure non è attualmente supportata.

4. Specificare un URL BLOB accessibile pubblicamente per un determinato codice di stato di errore e fare clic su **Salva**. Il gateway applicazione è ora configurato con la pagina di errore personalizzata.

   ![Codici di errore del gateway applicazione](media/custom-error/ag-error-codes.png)
## <a name="next-steps"></a>Passaggi successivi
Per informazioni sulla diagnostica del gateway applicazione, vedere [Integrità back-end, log di diagnostica e metriche per il gateway applicazione](application-gateway-diagnostics.md).