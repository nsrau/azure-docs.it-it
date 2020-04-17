---
title: Scaricare un elenco di utenti nel portale di Azure Active Directory . Documenti Microsoft
description: Scaricare i record utente in blocco nell'interfaccia di amministrazione di Azure in Azure Active Directory.Download user records in bulk in the Azure admin center in Azure Active Directory.
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 04/16/2020
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b35163387ed4ce71f7a2019835a1d9fdbff3051
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81532646"
---
# <a name="download-a-list-of-users-in-azure-active-directory-portal"></a>Scaricare un elenco di utenti nel portale di Azure Active DirectoryDownload a list of users in Azure Active Directory portal

Azure Active Directory (Azure AD) supporta le operazioni di importazione (creazione) di utenti in blocco.

## <a name="required-permissions"></a>Autorizzazioni necessarie

Per scaricare l'elenco di utenti dall'interfaccia di amministrazione di Azure AD, è necessario accedere con un utente assegnato a uno o più ruoli di amministratore a livello di organizzazione in Azure AD (L'amministratore utente è il ruolo minimo richiesto). L'invito ospite e lo sviluppatore di applicazioni non sono considerati ruoli di amministratore.

## <a name="to-download-a-list-of-users"></a>Per scaricare un elenco di utenti

1. [Accedere all'organizzazione di Azure AD](https://aad.portal.azure.com) con un account di amministratore utente nell'organizzazione.
2. Passare ad Azure Active Directory > utenti. Quindi selezionare gli utenti che si desidera includere nel download spuntando la casella nella colonna di sinistra accanto a ogni utente. Nota: al momento, non è possibile selezionare tutti gli utenti per l'esportazione. Ognuna di esse deve essere selezionata singolarmente.
3. In Azure AD selezionare **Utenti** > **Scarica utenti**.
4. Nella pagina **Scarica utenti** selezionare **Avvia** per ricevere un file CSV in cui sono elencate le proprietà del profilo utente. Se sono presenti errori, è possibile scaricare e visualizzare il file dei risultati nella pagina Risultati operazione in blocco. Il file contiene il motivo di ogni errore.

   ![Selezionare la posizione in cui si desidera scaricare l'elenco](./media/users-bulk-download/bulk-download.png)

   Il file di download conterrà l'elenco filtrato di utenti.

   Sono inclusi i seguenti attributi utente:

   - userPrincipalName
   - displayName
   - surname
   - mail
   - givenName
   - objectId
   - userType
   - jobTitle
   - department
   - accountEnabled
   - usageLocation
   - streetAddress
   - state
   - country
   - physicalDeliveryOfficeName
   - city
   - postalCode
   - telephoneNumber
   - mobile
   - authenticationPhoneNumber
   - authenticationAlternativePhoneNumber
   - authenticationEmail (autenticazioneE-mail
   - alternateEmailAddress (indirizzo emailalternativo)
   - ageGroup
   - consentProvidedForMinor
   - legalAgeGroupClassification

## <a name="check-status"></a>Controlla stato

È possibile visualizzare lo stato delle richieste bulk in sospeso nella pagina **Risultati dell'operazione** in blocco.

[![](media/users-bulk-download/bulk-center.png "Check status in the Bulk Operations Results page")](media/users-bulk-download/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>Limiti del servizio di download in blocco

Ogni attività in blocco per creare un elenco di utenti può essere eseguita per un massimo di un'ora. Ciò consente la creazione e il download di un elenco di almeno 500.000 utenti.

## <a name="next-steps"></a>Passaggi successivi

- [Aggiungere utenti in blocco](users-bulk-add.md)
- [Eliminare in blocco gli utenti](users-bulk-delete.md)
- [Utenti di ripristino in blocco](users-bulk-restore.md)
