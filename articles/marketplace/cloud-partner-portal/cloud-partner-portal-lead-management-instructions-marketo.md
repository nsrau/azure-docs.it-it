---
title: Istruzioni relative alla gestione dei lead per Marketo in Azure Marketplace | Microsoft Docs
description: Questo articolo illustra le istruzioni dettagliate per configurare la gestione dei lead con Marketo.
services: cloud-partner-portal
documentationcenter: 
author: Bigbrd
manager: hamidm
ms.service: cloud-partner-portal
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2017
ms.author: brdi
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 77c9bdf6b2634e222592ac0818f608ee3cb3fe09
ms.lasthandoff: 04/18/2017


---

# <a name="lead-management-instructions-for-marketo"></a>Istruzioni relative alla gestione dei lead per Marketo

Questo documento illustra come configurare il sistema Marketo in modo che Microsoft possa fornire lead di vendita. 

1. Accedere a "Design Studio" in Marketo. <br/>
![Immagine di configurazione di Marketo](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo1.png)

2. Fare clic su "New Form" (Nuovo modulo) <br/>
![Immagine del nuovo modulo di Marketo](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo2.png)

3. Compilare i campi nella finestra popup New Form (Nuovo modulo) <br/>
![Immagine della creazione modulo di Marketo](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo3.png)

4. Fare clic su "Finish" (Fine) nel modulo successivo. Non preoccuparsi della formattazione o dei campi. <br/>
![Immagine di completamento modulo di Marketo](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo4.png)

5. Approvare e chiudere.

6. Nella pagina successiva accedere alla sezione Embed Code (Codice di incorporamento).<br/>
![Immagine del codice di incorporamento di Marketo](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo5.png)

7. Verrà visualizzato un codice di incorporamento simile all'esempio seguente: 

   ` <script src="//app-ys12.marketo.com/js/forms2/js/forms2.min.js"></script>`

    `<form id="mktoForm_1179"></form> `

    `<script>MktoForms2.loadForm("("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>`

8. Utilizzare i valori disponibili nel codice di incorporamento per compilare i campi relativi a <b>ID server, ID Munchkin e ID del modulo</b> di Marketo nel portale per Cloud Partner. <br\> <br/>

  L'esempio seguente consente di individuare più facilmente gli ID dell'esempio precedente. <br\>

  serverId = **ys12** <br\>

 munchkinId = **123-PQR-789** <br\>

 formId = **1179** <br\> <br/>

