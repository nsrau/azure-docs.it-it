---
title: Eliminare Servizi di dominio Azure Active Directory Documenti Microsoft
description: Informazioni su come disabilitare o eliminare un dominio gestito di Servizi di dominio Azure Active Directory usando il portale di AzureLearn how to disable, or delete, an Azure Active Directory Domain Services managed domain using the Azure portal
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 89e407e1-e1e0-49d1-8b89-de11484eee46
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: 595436daa2efbd8e706a539d0a89c3ea98be31ff
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655472"
---
# <a name="delete-an-azure-active-directory-domain-services-managed-domain-using-the-azure-portal"></a>Eliminare un dominio gestito di Servizi di dominio Azure Active Directory tramite il portale di AzureDelete an Azure Active Directory Domain Services managed domain using the Azure portal

Se un dominio gestito non è più necessario, è possibile eliminare un'istanza di Servizi di dominio Azure Active Directory (Azure AD DS). Non è disponibile alcuna opzione per disattivare o disabilitare temporaneamente un dominio gestito di Servizi di dominio Active Directory di Azure.There's no option to turn off or temporarily disable an Azure AD DS managed domain. L'eliminazione del dominio gestito di Servizi di dominio Active Directory di Azure non comporta l'eliminazione o un impatto negativo sul tenant di Azure AD. Questo articolo illustra come usare il portale di Azure per eliminare un dominio gestito di Servizi di dominio Active Directory di Azure.This article shows you how to use the Azure portal to delete an Azure AD DS managed domain.

> [!WARNING]
> **L'eliminazione è permanente e non può essere annullata.**
> Quando si elimina un dominio gestito di Servizi di dominio Active Directory di Azure, si verificano i passaggi seguenti:When you delete an Azure AD DS managed domain, the following steps occur:
>   * Viene effettuato il deprovisioning dei controller di dominio per il dominio gestito e vengono rimossi dalla rete virtuale.
>   * I dati del dominio gestito vengono eliminati definitivamente, Questi dati includono gestione delle sole personalizzate, oggetti Criteri di gruppo, record DNS personalizzati, entità servizio, GMSA e così via creati.
>   * I computer aggiunti al dominio gestito perdono la relazione di trust con il dominio e devono essere separati dal dominio.
>       * Non è possibile accedere a questi computer utilizzando le credenziali di Active Directory aziendali. È invece necessario utilizzare le credenziali di amministratore locale per il computer.

## <a name="delete-the-managed-domain"></a>Eliminare il dominio gestito

Per eliminare un dominio gestito di Servizi di dominio Active Directory di Azure, completare i passaggi seguenti:To delete an Azure AD DS managed domain, complete the following steps:

1. Nel portale di Azure cercare e selezionare Servizi di **dominio Azure AD.**
1. Selezionare il nome del dominio gestito di Servizi di dominio Active Directory di Azure, ad esempio *aaddscontoso.com*.
1. Nella pagina **Panoramica** selezionare **Elimina**. Per confermare l'eliminazione, digitare nuovamente il nome di dominio del dominio gestito, quindi selezionare **Elimina**.

L'eliminazione del dominio gestito di Servizi di dominio Active Directory di Azure può richiedere 15-20 minuti o più.

## <a name="next-steps"></a>Passaggi successivi

Valutare la possibilità di [condividere commenti e suggerimenti][feedback] per le funzionalità che si desidera visualizzare in Servizi di dominio Active Directory di Azure.Consider sharing feedback for the features that you would to see in Azure AD DS.

Se si vuole iniziare di nuovo a usare Servizi di dominio Azure AD, vedere [Creare e configurare un'istanza][create-instance]di Servizi di dominio Azure Active Directory.

<!-- INTERNAL LINKS -->
[feedback]: contact-us.md
[create-instance]: tutorial-create-instance.md
