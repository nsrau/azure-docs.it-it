---
title: Trasferire la proprietà della fatturazione di una sottoscrizione di Azure
description: Descrive come trasferire la proprietà della fatturazione di una sottoscrizione di Azure a un altro account.
keywords: trasferire la sottoscrizione di Azure, trasferimento della sottoscrizione di Azure, spostare una sottoscrizione di Azure in un altro account, cambiare il proprietario della sottoscrizione di Azure, trasferire la sottoscrizione di Azure in un altro account, trasferire la fatturazione di Azure
author: bandersmsft
ms.reviewer: amberb
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 09/23/2020
ms.author: banders
ms.custom: contperfq1
ms.openlocfilehash: 1fedad7dac4c253c68a0f27d0e0470123833a3be
ms.sourcegitcommit: 67e8e1caa8427c1d78f6426c70bf8339a8b4e01d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2020
ms.locfileid: "91666110"
---
# <a name="transfer-billing-ownership-of-an-azure-subscription-to-another-account"></a>Trasferire la proprietà della fatturazione di una sottoscrizione di Azure in un altro account

Questo articolo descrive i passaggi necessari per trasferire la proprietà della fatturazione di una sottoscrizione di Azure a un altro account. Prima di trasferire la proprietà della fatturazione per una sottoscrizione, leggere [Informazioni sul trasferimento della proprietà della fatturazione per una sottoscrizione di Azure](../understand/subscription-transfer.md).

Se si vuole mantenere la proprietà della fatturazione, ma cambiare il tipo di sottoscrizione, vedere [Trasferire la sottoscrizione di Azure a un'altra offerta](switch-azure-offer.md). Per controllare chi può accedere alle risorse della sottoscrizione, vedere [Ruoli predefiniti di Azure](../../role-based-access-control/built-in-roles.md).

Per i clienti con Contratto Enterprise, gli amministratori dell'organizzazione possono trasferire la proprietà della fatturazione delle sottoscrizioni tra gli account. Per altre informazioni, vedere [Modificare il proprietario dell'account](ea-portal-get-started.md#change-account-owner).

Solo l'amministratore della fatturazione di un account può trasferire la proprietà di una sottoscrizione.

## <a name="transfer-billing-ownership-of-an-azure-subscription"></a>Trasferire la proprietà della fatturazione di una sottoscrizione di Azure

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore dell'account di fatturazione che include la sottoscrizione da trasferire. Se non si è certi di avere il ruolo di amministratore o se è necessario determinare chi ha questo ruolo, vedere [Determinare l'amministratore della fatturazione dell'account](../understand/subscription-transfer.md#whoisaa).
1. Cercare **Gestione dei costi e fatturazione**.  
   ![Screenshot che mostra una ricerca nel portale di Azure](./media/billing-subscription-transfer/billing-search-cost-management-billing.png)
1. Selezionare **Sottoscrizioni** nel riquadro sinistro. A seconda dell'accesso, potrebbe essere necessario selezionare un ambito di fatturazione e quindi **Sottoscrizioni** o **Sottoscrizioni di Azure**.
1. Selezionare **Trasferisci la proprietà della fatturazione** per la sottoscrizione da trasferire.  
   ![Selezionare la sottoscrizione da trasferire](./media/billing-subscription-transfer/billing-select-subscription-to-transfer.png)
1. Immettere l'indirizzo di posta elettronica di un utente con il ruolo di amministratore della fatturazione dell'account che diventerà il nuovo proprietario della sottoscrizione.
1. Se la sottoscrizione viene trasferita in un account in un altro tenant di Azure AD, scegliere se la si vuole spostare nel tenant del nuovo account. Per altre informazioni, vedere [Trasferimento della sottoscrizione in un account di un altro tenant di Azure AD](#transfer-a-subscription-to-another-azure-ad-tenant-account).
    > [!IMPORTANT]
    > Se si sceglie di spostare la sottoscrizione nel tenant di Azure AD del nuovo account, tutte le [assegnazioni di ruolo di Azure](../../role-based-access-control/role-assignments-portal.md) per l'accesso alle risorse nella sottoscrizione vengono rimosse definitivamente. Solo l'utente del nuovo account che accetta la richiesta di trasferimento avrà accesso per la gestione delle risorse della sottoscrizione. In alternativa, è possibile deselezionare l'opzione **Tenant Azure AD della sottoscrizione** per trasferire la proprietà di fatturazione senza spostare la sottoscrizione nel tenant del nuovo account. In tal caso, le assegnazioni di ruolo di Azure esistenti per l'accesso alle risorse di Azure verranno mantenute.  
    ![Pagina di invio della richiesta di trasferimento](./media/billing-subscription-transfer/billing-send-transfer-request.png)
1. Selezionare **Invia la richiesta di trasferimento**.
1. L'utente riceve un messaggio di posta elettronica con le istruzioni per esaminare la richiesta di trasferimento.  
   ![Messaggio di posta elettronica sul trasferimento della sottoscrizione inviato al destinatario](./media/billing-subscription-transfer/billing-receiver-email.png)
1. Per approvare la richiesta di trasferimento, l'utente seleziona il collegamento nel messaggio di posta elettronica e segue le istruzioni. L'utente seleziona quindi un metodo di pagamento che verrà usato per pagare la sottoscrizione. Se l'utente non ha un account Azure, dovrà iscriversi per riceverne uno nuovo.  
   ![Pagina Web del trasferimento della prima sottoscrizione](./media/billing-subscription-transfer/billing-accept-ownership-step1.png)
   ![Pagina Web del trasferimento della seconda sottoscrizione](./media/billing-subscription-transfer/billing-accept-ownership-step2.png)
   ![Pagina Web del trasferimento della terza sottoscrizione](./media/billing-subscription-transfer/billing-accept-ownership-step3.png)
1. Operazione riuscita. La sottoscrizione è stata trasferita.

## <a name="transfer-a-subscription-to-another-azure-ad-tenant-account"></a>Trasferire una sottoscrizione in un account di un altro tenant di Azure AD

Quando ci si iscrive ad Azure, viene automaticamente creato un tenant di Azure Active Directory (AD). Il tenant rappresenta l'account. Viene usato per gestire l'accesso alle sottoscrizioni e alle risorse.

Le nuove sottoscrizioni create vengono ospitate nel tenant di Azure AD del proprio account. Se si vuole consentire ad altri utenti di accedere alla sottoscrizione o alle relative risorse, è necessario invitarli ad aggiungersi al tenant. In questo modo è possibile controllare l'accesso alle sottoscrizioni e alle risorse.

Se la proprietà della fatturazione della sottoscrizione viene trasferita in un account in un altro tenant di Azure AD, scegliere se la si vuole spostare nel tenant del nuovo account. In questo caso, tutti gli utenti, i gruppi o le entità servizio che avevano [assegnazioni di ruolo di Azure](../../role-based-access-control/role-assignments-portal.md) per la gestione delle sottoscrizioni e delle relative risorse perderanno l'accesso. Solo l'utente del nuovo account che accetta la richiesta di trasferimento avrà accesso per la gestione delle risorse. Il nuovo proprietario deve aggiungere manualmente questi utenti alla sottoscrizione per fornire l'accesso agli utenti che lo hanno perso. Per altre informazioni, vedere [Trasferire una sottoscrizione di Azure in una directory di Azure AD diversa](../../role-based-access-control/transfer-subscription.md).

## <a name="transfer-visual-studio-and-partner-network-subscriptions"></a>Trasferire le sottoscrizioni di Visual Studio e Partner Network

Alle sottoscrizioni di Visual Studio e Microsoft Partner Network è associato un credito Azure ricorrente mensile. Quando si trasferiscono queste sottoscrizioni, il credito non è disponibile nell'account di fatturazione di destinazione. La sottoscrizione usa il credito nell'account di fatturazione di destinazione. Supponiamo ad esempio che Bob trasferisca una sottoscrizione di Visual Studio Enterprise all'account di Jane il 9 settembre e che Jane accetti il trasferimento. Al termine del trasferimento, la sottoscrizione inizia a usare il credito nell'account di Jane. Il credito viene reimpostato ogni nono giorno del mese.

## <a name="next-steps-after-accepting-billing-ownership"></a>Passaggi successivi all'accettazione della proprietà della fatturazione

Se è stata accettata la proprietà di fatturazione di una sottoscrizione di Azure, è consigliabile rivedere i passaggi successivi seguenti:

1. Rivedere e aggiornare il ruolo dell'amministratore del servizio, il ruolo dei coamministratori e le assegnazioni di ruolo di Azure. Per altre informazioni, vedere [Aggiungere o modificare gli amministratori delle sottoscrizioni di Azure](add-change-subscription-administrator.md) e [Aggiungere o rimuovere assegnazioni di ruolo di Azure usando il portale di Azure](../../role-based-access-control/role-assignments-portal.md).
1. Aggiornare le credenziali associate ai servizi della sottoscrizione, tra cui:
   1. Certificati di gestione che concedono all'utente privilegi di amministratore per le risorse della sottoscrizione. Per altre informazioni, vedere [Creare e caricare un certificato di gestione per Azure](../../cloud-services/cloud-services-certs-create.md)
   1. Chiavi di accesso per servizi quali Archiviazione. Per altre informazioni, vedere [Informazioni sugli account di archiviazione di Azure](../../storage/common/storage-create-storage-account.md).
   1. Credenziali di accesso remoto per servizi quali macchine virtuali di Azure.
1. Se si lavora con un partner, è consigliabile aggiornare l'ID partner nella sottoscrizione. È possibile aggiornare l'ID partner nel [portale di Azure](https://portal.azure.com). Per altre informazioni, vedere [Collegare un ID partner agli account Azure](link-partner-id.md)

## <a name="troubleshooting"></a>Risoluzione dei problemi

Se si verificano problemi durante il trasferimento delle sottoscrizioni, usare le informazioni sulla risoluzione dei problemi seguenti.

### <a name="the-transfer-subscription-option-is-unavailable"></a>L'opzione Trasferisci sottoscrizione non è disponibile

<a name="no-button"></a> 

Il trasferimento della sottoscrizione in modalità self-service non è disponibile per questo account di fatturazione. Attualmente, nel portale di Azure non è supportato il trasferimento della proprietà della fatturazione delle sottoscrizioni negli account del contratto Enterprise Agreement (EA). Inoltre, gli account con contratto del cliente Microsoft creati in collaborazione con un rappresentante Microsoft non supportano il trasferimento della proprietà della fatturazione.

###  <a name="not-all-subscription-types-can-transfer"></a>Non tutti i tipi di sottoscrizione supportano il trasferimento

Non tutti i tipi di sottoscrizione supportano il trasferimento della proprietà della fatturazione. Per visualizzare l'elenco dei tipi che li supportano, vedere [Tipi di sottoscrizione supportati](../understand/subscription-transfer.md#supported-subscription-types).

###  <a name="access-denied-error-shown-when-trying-to-transfer-subscription-billing-ownership"></a>Errore di accesso negato visualizzato quando si tenta il trasferimento della proprietà della fatturazione

Questo errore viene visualizzato se si prova a trasferire una sottoscrizione di un piano di Microsoft Azure e non si hanno le autorizzazioni necessarie. Per trasferire una sottoscrizione di un piano di Microsoft Azure, è necessario essere un proprietario o un collaboratore nella sezione della fattura in cui viene addebitata la sottoscrizione. Per altre informazioni, vedere la sezione [Gestire le sottoscrizioni per la sezione della fattura](../manage/understand-mca-roles.md#manage-subscriptions-for-invoice-section).

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi

- Rivedere e aggiornare il ruolo dell'amministratore del servizio, il ruolo dei coamministratori e le assegnazioni di ruolo di Azure. Per altre informazioni, vedere [Aggiungere o modificare gli amministratori delle sottoscrizioni di Azure](add-change-subscription-administrator.md) e [Aggiungere o rimuovere assegnazioni di ruolo di Azure usando il portale di Azure](../../role-based-access-control/role-assignments-portal.md).
