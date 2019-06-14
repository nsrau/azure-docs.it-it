---
title: Connettersi ad Azure Analysis Services con Excel | Microsoft Docs
description: Informazioni su come connettersi a un server di Azure Analysis Services usando Excel.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 5c46d4e4d23744cf07ccf7857a33990bf405a6a1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61023258"
---
# <a name="connect-with-excel"></a>Connettersi con Excel

Dopo aver creato un server e aver distribuito un modello tabulare nel server, i client possono connettersi e iniziare l'esplorazione dei dati. 

## <a name="before-you-begin"></a>Prima di iniziare

L'account con cui si esegue l'accesso deve appartenere a un ruolo del database modello con almeno le autorizzazioni di lettura. Per altre informazioni, vedere [Autenticazione e autorizzazioni utente](analysis-services-manage-users.md). 

## <a name="connect-in-excel"></a>Connettersi in Excel

La connessione a un server in Excel è supportata con la funzione Dati in Excel 2016 e versioni successive. La connessione tramite l'Importazione guidata tabella in Power Pivot non è supportata. 

1. Sulla barra multifunzione **Dati** di Excel fare clic su **Recupera dati esterni** > **Da altre origini** > **Da Analysis Services**.

2. Nella Connessione guidata dati, in **Nome Server**, immettere il nome del server, inclusi il protocollo e l'URI. Ad esempio, asazure://westcentralus.asazure.windows.net/advworks. In **Credenziali di accesso** selezionare **Usa nome utente e password seguenti** e quindi digitare il nome utente dell'organizzazione, ad esempio nancy@adventureworks.com, e la password.

    > [!IMPORTANT]
    > Se si accede con un account Microsoft, con un Live ID o con Yahoo, Gmail e così via, o se si deve accedere tramite autenticazione a più fattori, lasciare vuoto il campo della password. Dopo aver fatto clic su Avanti, è necessario specificare la password. 

    ![Connettersi dall'accesso a Excel](./media/analysis-services-connect-excel/aas-connect-excel-logon.png)

3. In **Seleziona database e tabella** selezionare il database e il modello o la prospettiva e quindi fare clic su **Fine**.
   
    ![Connettersi dal modello di selezione in Excel](./media/analysis-services-connect-excel/aas-connect-excel-select.png)


## <a name="see-also"></a>Vedere anche

[Librerie client](analysis-services-data-providers.md)   
[Gestire il server](analysis-services-manage.md)     


