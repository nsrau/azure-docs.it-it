---
title: Scaricare un elenco di utenti nel portale di Azure Active Directory | Microsoft Docs
description: Scaricare in blocco i record utente nell'interfaccia di amministrazione di Azure in Azure Active Directory.
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 04/16/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: cb119ef0ffd4da4dc524c9d3c0a88b94e2251142
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87423559"
---
# <a name="download-a-list-of-users-in-azure-active-directory-portal"></a>Scaricare un elenco di utenti nel portale Azure Active Directory

Azure Active Directory (Azure AD) supporta operazioni di importazione (creazione) dell'utente in blocco.

## <a name="required-permissions"></a>Autorizzazioni necessarie

Per scaricare l'elenco di utenti dall'interfaccia di amministrazione di Azure AD, è necessario aver eseguito l'accesso con un utente assegnato a uno o più ruoli di amministratore a livello di organizzazione in Azure AD (l'amministratore utente è il ruolo minimo richiesto). L'invito Guest e lo sviluppatore di applicazioni non sono considerati ruoli di amministratore.

## <a name="to-download-a-list-of-users"></a>Per scaricare un elenco di utenti

1. [Accedere all'organizzazione Azure ad](https://aad.portal.azure.com) con un account amministratore utente nell'organizzazione.
2. Passare a Azure Active Directory > utenti. Quindi selezionare gli utenti che si desidera includere nel download selezionando la casella nella colonna a sinistra accanto a ogni utente. Nota: al momento non è possibile selezionare tutti gli utenti per l'esportazione. Ognuna di esse deve essere selezionata singolarmente.
3. In Azure ad selezionare **utenti**  >  **Scarica utenti**.
4. Nella pagina **Scarica utenti** selezionare **inizia** a ricevere un file CSV che elenca le proprietà del profilo utente. Se sono presenti errori, è possibile scaricare e visualizzare il file dei risultati nella pagina Risultati dell'operazione in blocco. Il file contiene il motivo di ogni errore.

   ![Selezionare la posizione desiderata per l'elenco degli utenti che si desidera scaricare](./media/users-bulk-download/bulk-download.png)

   Il file di download conterrà l'elenco filtrato di utenti.

   Sono inclusi gli attributi utente seguenti:

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
   - authenticationEmail
   - alternateEmailAddress
   - ageGroup
   - consentProvidedForMinor
   - legalAgeGroupClassification

## <a name="check-status"></a>Controlla stato

È possibile visualizzare lo stato delle richieste bulk in sospeso nella pagina **risultati operazione bulk** .

[![Verificare lo stato nella pagina Risultati operazioni bulk.](media/users-bulk-download/bulk-center.png)](media/users-bulk-download/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>Limiti del servizio di download bulk

Ogni attività bulk per la creazione di un elenco di utenti può essere eseguita per un massimo di un'ora. In questo modo è possibile creare e scaricare un elenco di almeno 500.000 utenti.

## <a name="next-steps"></a>Passaggi successivi

- [Aggiungere utenti in blocco](users-bulk-add.md)
- [Eliminazione di utenti in blocco](users-bulk-delete.md)
- [Ripristinare utenti in blocco](users-bulk-restore.md)
