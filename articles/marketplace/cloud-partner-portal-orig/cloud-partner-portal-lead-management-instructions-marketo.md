---
title: Marketo | Microsoft Docs
description: Configurare la gestione dei lead per Marketo.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: pbutlerm
ms.openlocfilehash: 482d7a9662a79503bb2b197d5a6c63c9fa3c1c96
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58113113"
---
# <a name="configure-lead-management-in-marketo"></a>Configurare la gestione dei lead in Marketo

Questo articolo descrive come configurare Marketo per gestire i lead di vendita Microsoft.

1. Accedere a Marketo.
2. Selezionare **Design Studio**.
    ![Marketo Design Studio](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo1.png)

3.  Selezionare **Nuovo modulo**.
    ![Nuovo modulo di Marketo](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo2.png)

4.  Compilare i campi obbligatori nel nuovo modulo e quindi selezionare **Crea**.
    ![Creazione nuovo modulo di Marketo](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo3.png)

4.  Nei dettagli del campo selezionare **Fine**.
    ![Completamento modulo di Marketo](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo4.png)

5.  Approvare e chiudere.

6.  Nella scheda MarketplaceLeadBacked selezionare **Codice di incorporamento**.
    ![Opzione Codice di incorporamento di Marketo](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo5.png)

7.  L'opzione Codice di incorporamento di Marketo visualizza un codice simile all'esempio seguente.

`<script src="//app-ys12.marketo.com/js/forms2/js/forms2.min.js"></script>`

    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>

1. Copiare i valori visualizzati in Codice di incorporamento per configurare **ID server**, **ID Munchkin** e **ID del modulo** nei campi di Marketo nel portale Cloud Partner.

Usare l'esempio seguente come guida per ottenere gli ID necessari dall'esempio di codice di incorporamento di Marketo.

- ID server = **ys12**
- ID Munchkin = **123-PQR-789**
- ID modulo = **1179**\
