---
title: Identità gestite per le risorse di Azure
description: Panoramica delle identità gestite per le risorse di Azure.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 0232041d-b8f5-4bd2-8d11-27999ad69370
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: overview
ms.custom: mvc
ms.date: 05/20/2020
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: aeeb7b2704474e030e00eda03e73fd523434a207
ms.sourcegitcommit: 9bfd94307c21d5a0c08fe675b566b1f67d0c642d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/17/2020
ms.locfileid: "84976121"
---
# <a name="what-are-managed-identities-for-azure-resources"></a>Informazioni sulle identità gestite per le risorse di Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Gli sviluppatori cloud cercano il metodo più semplice e sicuro per accedere alle risorse di Azure nel codice. 

Le identità gestite per le risorse di Azure consentono di soddisfare questo requisito perché:

- **Eliminano** la necessità di credenziali nel codice.
- **Ruotano** automaticamente le credenziali.
- **Riducono** al minimo il coinvolgimento dello sviluppatore nella gestione delle identità.


## <a name="how-it-works"></a>Funzionamento 

Tutte le risorse di Azure che supportano le identità gestite possono ottenere token per lo scambio di dati senza includere le credenziali nel codice. Questo processo comprende i passaggi seguenti:

 
1.  **Abilitare**: creare l'identità gestita per la risorsa.
2.  **Concedere l'accesso**: consentire l'accesso alle risorse con il controllo degli accessi in base al ruolo di Azure.
3.  **Accedere**: eseguire le azioni consentite.
4.  **Disabilitare**: eliminare l'identità gestita. 

## <a name="managed-identity-types"></a>Tipi di identità gestita

Sono disponibili due tipi di identità gestite:

- Identità gestita assegnata dal sistema

- Identità gestita assegnata dall'utente


Per le risorse di Azure autonome, è possibile abilitare identità gestite **assegnate dal sistema**. Le identità gestite assegnate dal sistema offrono il supporto più pratico dal punto di vista della gestione delle identità. Con un solo clic, è possibile abilitare la gestione automatizzata del ciclo di vita di un'identità per la risorsa.   

 ![Identità gestita assegnata dal sistema](./media/overview/system-assigned.png)  

Sebbene le identità gestite assegnate dal sistema forniscano la soluzione più pratica per le risorse autonome, lo scenario cambia se è necessario gestire un gruppo di risorse di Azure per la stessa attività. In questo scenario è preferibile creare manualmente un'identità e assegnare questa identità master a tutte le risorse di Azure che è necessario raggruppare. Questa assegnazione è nota come identità gestita **assegnata dall'utente**. 
  

## <a name="supported-services"></a>Servizi supportati

È possibile usare le identità gestite per le risorse di Azure per eseguire l'autenticazione ai servizi che supportano l'autenticazione di Azure AD. Per un elenco dei servizi di Azure che supportano la funzionalità delle identità gestite per le risorse di Azure, vedere [Servizi che supportano le identità gestite per le risorse di Azure](services-support-msi.md).


## <a name="next-steps"></a>Passaggi successivi

È possibile iniziare a usare la funzionalità delle identità gestite per le risorse di Azure con le guide introduttive seguenti:

* [Usare un'identità gestita assegnata dal sistema per una macchina virtuale Windows per accedere a Resource Manager](tutorial-windows-vm-access-arm.md)
* [Usare un'identità gestita assegnata dal sistema per una macchina virtuale Linux per accedere a Resource Manager](tutorial-linux-vm-access-arm.md)
