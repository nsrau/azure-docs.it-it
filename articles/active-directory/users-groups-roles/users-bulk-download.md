---
title: Scaricare un elenco di utenti (anteprima) nel portale di Azure Active Directory | Microsoft Docs
description: Scaricare in blocco i record utente nell'interfaccia di amministrazione di Azure in Azure Active Directory.
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 07/15/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: d106e803fdef4f054b7e4717ba59a43359d1d68a
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720294"
---
# <a name="download-a-list-of-users-preview-in-azure-active-directory-portal"></a>Scaricare un elenco di utenti (anteprima) nel portale di Azure Active Directory

Azure Active Directory (Azure AD) supporta operazioni di importazione (creazione) dell'utente in blocco.

## <a name="required-permissions"></a>Autorizzazioni necessarie

Per scaricare l'elenco di utenti dall'interfaccia di amministrazione di Azure AD, è necessario aver eseguito l'accesso con un utente assegnato a uno o più ruoli di amministratore a livello di organizzazione nel Azure AD. L'invito Guest e lo sviluppatore di applicazioni non sono considerati ruoli di amministratore.

## <a name="to-download-a-list-of-users"></a>Per scaricare un elenco di utenti

1. [Accedere all'organizzazione Azure ad](https://aad.portal.azure.com) con un account amministratore utente nell'organizzazione.
2. Passare a Azure Active Directory > utenti. Quindi selezionare gli utenti che si desidera includere nel download selezionando la casella nella colonna a sinistra accanto a ogni utente. Nota: al momento non è possibile selezionare tutti gli utenti per l'esportazione. Ognuna di esse deve essere selezionata singolarmente.
3. In Azure AD selezionare **utenti** > **Scarica utenti**.
4. Nella pagina **Scarica utenti** selezionare **inizia** a ricevere un file CSV che elenca le proprietà del profilo utente. Se sono presenti errori, è possibile scaricare e visualizzare il file dei risultati nella pagina risultati operazione bulk. Il file contiene il motivo per ogni errore.

   ![Selezionare la posizione desiderata per l'elenco degli utenti che si desidera scaricare](./media/users-bulk-download/bulk-download.png)

   Il file di download conterrà l'elenco filtrato di utenti.

   Verranno inclusi gli attributi utente seguenti: 

   - userPrincipalName
   - displayName
   - surname
   - mail
   - givenName
   - objectId
   - userType
   - jobTitle
   - department
   - manager
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

È possibile visualizzare lo stato delle richieste bulk in sospeso nella pagina **risultati operazione bulk (anteprima)** .

   ![Controllare lo stato di caricamento nella pagina Risultati operazioni bulk](./media/users-bulk-download/bulk-center.png)

## <a name="bulk-download-service-limits"></a>Limiti del servizio di download bulk

Ogni attività bulk per la creazione di un elenco di utenti può essere eseguita per un massimo di un'ora. In questo modo è possibile creare e scaricare un elenco di almeno 500.000 utenti.

## <a name="next-steps"></a>Passaggi successivi

- [Aggiungere utenti in blocco](users-bulk-add.md)
- [Eliminare gli utenti in blocco](users-bulk-delete.md)
- [Ripristino in blocco degli utenti](users-bulk-restore.md)
