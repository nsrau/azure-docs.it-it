---
title: Introduzione al playbook PoC di Azure Active Directory | Microsoft Docs
description: "Esplorare e implementare rapidamente gli scenari di Gestione delle identità e degli accessi"
services: active-directory
keywords: azure active directory, studio, modello di verifica, PoC
documentationcenter: 
author: dstefanMSFT
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: dstefan
ms.openlocfilehash: 80b7ba9507b85621e7b0623362774edc0c8cc729
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-proof-of-concept-playbook-introduction"></a>Playbook dei modelli di verifica di Azure Active Directory: introduzione

In questo articolo vengono fornite linee guida per l'esplorazione di diverse funzionalità di Azure AD in un Proof of Concept (PoC). I destinatari di questo documento sono gli architetti di identità, i professionisti IT e gli integratori di sistemi

## <a name="how-to-use-this-playbook"></a>Come usare questo playbook

1. Usare la sezione [Tema](active-directory-playbook-ingredients.md#theme) e selezionare le aree di interesse in base alle esigenze.  
2. Definire l'ambito del PoC scegliendo gli scenari in linea con gli obiettivi aziendali. L'ambito deve essere il più ristretto possibile. Si consiglia di definire un ambito il più breve e conciso possibile in modo da veicolarne il valore alle parti interessate, riducendone al minimo la complessità di realizzazione.  
3. Usare la sezione [Implementazione](active-directory-playbook-implementation.md) per comprendere gli scenari e il relativo significato per l'ambiente. In ogni scenario viene descritto come eseguire la configurazione (i cosiddetti [blocchi predefiniti](active-directory-playbook-building-blocks.md)) e come passare da uno scenario all'altro. 
4. In ogni blocco predefinito vengono illustrati i prerequisiti necessari, nonché il tempo approssimativo necessario per il completamento. Ciò costituisce un valido supporto durante il processo di pianificazione. 
5. In base ai passaggi da 1 a 3 sopra riportati, definire l'[ambiente](active-directory-playbook-ingredients.md#environment) in cui si desidera eseguire le operazioni. Si consiglia di creare un ambiente di produzione che offra la migliore esperienza possibile per gli utenti. 
6. In presenza di requisiti in conflitto, utilizzare questa utile matrice per trovare un compromesso 
   * Visualizzazione incentrata sul tema del valore  
   * Precisione nella preparazione, configurazione ed esecuzione degli scenari 
   * Tempo minimo richiesto per eseguire gli scenari di destinazione 
   * Esecuzione in linea il più possibile alla produzione all'interno dei vincoli 

>[!NOTE]
> In questo articolo, verranno mostrate alcune applicazioni e prodotti di terze parti specifici citati a titolo esemplificativo per comodità. Azure AD supporta migliaia di applicazioni incluse nella [raccolta delle applicazioni](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps) che è possibile utilizzare in base alle esigenze e all'ambiente. 



[!INCLUDE [active-directory-playbook-toc](../../includes/active-directory-playbook-steps.md)]