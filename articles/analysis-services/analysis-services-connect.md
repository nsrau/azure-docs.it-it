---
title: Ottenere dati da Azure Analysis Services | Documentazione Microsoft
description: Informazioni su come connettersi e ottenere dati da un server di Analysis Services in Azure.
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: b37f70a0-9166-4173-932d-935d769539d1
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 02/13/2017
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 3992e327bbe887338234fc2d516b053f0750a151
ms.openlocfilehash: 118ea84abb0de095ac515ee98a643718ca54c043
ms.lasthandoff: 02/16/2017


---
# <a name="get-data-from-azure-analysis-services"></a>Ottenere dati da Azure Analysis Services

Dopo aver creato un server in Azure e aver distribuito un modello tabulare nel server, gli utenti dell'organizzazione sono pronti per connettersi e iniziare l'esplorazione dei dati.

## <a name="data-providers-aka-client-libraries"></a>Provider di dati (noti anche come librerie client)

Le applicazioni client, ad esempio Power BI Desktop e Microsoft Excel, usano provider AMO, ADOMD.NET e OLE DB aggiornati per connettersi e interagire con Analysis Services. Con alcune versioni precedenti di Excel o applicazioni personalizzate, potrebbe essere necessario installare i provider di dati più recenti per la connessione ad Analysis Services di Azure. Per altre informazioni, vedere [Data providers](analysis-services-data-providers.md) (Provider di dati).

## <a name="server-name"></a>Nome server

Quando si crea un server di Analysis Services in Azure, si specifica un nome univoco e l'area in cui il server deve essere creato. Quando si specifica il nome del server in una connessione, lo schema di denominazione del server è il seguente:

```
<protocol>://<region>/<servername>
```
 Dove protocol è la stringa **asazure**, region è l'Uri dell'area in cui è stato creato il server (ad esempio, per gli Stati Uniti occidentali, westus.asazure.windows.net) e servername è il nome del server univoco all'interno dell'area.

## <a name="get-the-server-name"></a>Ottenere il nome del server

Prima di connettersi, è necessario ottenere il nome del server. Nel **portale di Azure** > server > **Panoramica** > **Nome server** copiare l'intero nome del server. Se anche altri utenti dell'organizzazione si connettono a questo server, è opportuno condividere il nome del server con loro. Quando si specifica un nome di server, è necessario usare l'intero percorso.

![Ottenere il nome del server in Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)

## <a name="connect-in-power-bi-desktop"></a>Connettersi in Power BI Desktop

> [!NOTE]
> Questa funzionalità è disponibile in anteprima.
> 
> 

1. In [Power BI Desktop](https://powerbi.microsoft.com/desktop/) fare clic su **Recupera dati** > **Database** > **Azure Analysis Services**.
2. In **Server** incollare il nome del server dagli Appunti.
3. In **Database**, se si conosce il nome della prospettiva o del database del modello tabulare a cui connettersi, incollarlo qui. In caso contrario, è possibile lasciare vuoto questo campo. È possibile selezionare un database o una prospettiva nella schermata successiva.
4. Lasciare selezionata l'impostazione predefinita dell'opzione **Connessione dinamica** e quindi scegliere **Connetti**. Se viene chiesto di specificare un account, immettere l'account dell'organizzazione.
5. In **Strumento di navigazione** espandere il server, selezionare il modello o la prospettiva a cui connettersi e quindi fare clic su **Connetti**. Se si fa clic su un modello o su una prospettiva, vengono mostrati tutti gli oggetti per la visualizzazione selezionata.

## <a name="connect-in-power-bi"></a>Connettersi in Power BI

1. Creare un file di Power BI Desktop che dispone di una connessione dinamica al modello sul server.
2. In [Power BI](https://powerbi.microsoft.com) fare clic su **Recupera dati** > **File**. Individuare e selezionare il file.

## <a name="connect-in-excel"></a>Connettersi in Excel

La connessione al server di Azure Analysis Services in Excel è supportata tramite Recupera dati in Excel 2016 o Power Query nelle versioni precedenti. [Provider MSOLAP.7](analysis-services-data-providers.md) è obbligatorio. La connessione tramite l'Importazione guidata tabella in Power Pivot non è supportata.

> [!NOTE]
> Alcune organizzazioni distribuiscono gli aggiornamenti di Office 365 su Deferred Channel; gli aggiornamenti relativi alla versione vengono posticipati di quattro mesi rispetto alla versione corrente. Per la versione di Excel 2016 con compilazione 1609.7369.2115 e versioni precedenti o per Excel 2013, è possibile creare un file Office Data Connection (con estensione odc) e aggiornare manualmente il provider MSOLAP.7 per la connessione a un server di Azure Analysis Services. Per altre informazioni, vedere [Create an .odc file](analysis-services-odc.md) (Creare un file con estensione odc).
> 
> 

**Per connettersi da Excel 2016**

1. In Excel 2016, sulla barra multifunzione **Dati**, fare clic su **Recupera dati esterni** > **Da altre origini** > **Da Analysis Services**.
2. Nella Connessione guidata dati, in **Nome Server**, incollare il nome del server dagli Appunti. In **Credenziali di accesso** selezionare **Usa nome utente e password seguenti** e quindi digitare il nome utente dell'organizzazione, ad esempio nancy@adventureworks.com,, e la password.

    ![Accesso per la connessione in Excel](./media/analysis-services-connect/aas-connect-excel-logon.png)
3. In **Seleziona database e tabella** selezionare il database e il modello o la prospettiva e quindi fare clic su **Fine**.
   
    ![Selezione del modello per la connessione in Excel](./media/analysis-services-connect/aas-connect-excel-select.png)

## <a name="connection-string"></a>Stringa di connessione

Quando ci si connette a Azure Analysis Services usando il modello a oggetti tabulare, usare i formati seguenti per la stringa di connessione:

###### <a name="integrated-azure-active-directory-authentication"></a>Autenticazione integrata di Azure Active Directory

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;"
```
L'autenticazione integrata seleziona la cache delle credenziali di Azure Active Directory, se disponibile. In caso contrario, viene visualizzata la finestra di accesso di Azure.

###### <a name="azure-active-directory-authentication-with-username-and-password"></a>Autenticazione di Azure Active Directory con nome utente e password

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;User ID=<user name>;Password=<password>;Persist Security Info=True; Impersonation Level=Impersonate;";
```

## <a name="next-steps"></a>Passaggi successivi

[Gestire il server](analysis-services-manage.md)


