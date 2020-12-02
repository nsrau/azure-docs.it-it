---
title: Connessione ai server di Azure Analysis Services | Microsoft Docs
description: Informazioni su come connettersi e ottenere dati da un server di Analysis Services in Azure.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: references_regions
ms.openlocfilehash: 4abe1e9c6f9d7b62792936f816b9c46a937be41a
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499425"
---
# <a name="connecting-to-servers"></a>Connessione ai server

Questo articolo descrive la connessione a un server usando la modellazione dei dati e le applicazioni di gestione come SQL Server Management Studio (SSMS) o Visual Studio con progetti Analysis Services o con applicazioni di creazione di report client come Microsoft Excel, Power BI Desktop o applicazioni personalizzate. Le connessioni ad Azure Analysis Services usano HTTPS.

## <a name="client-libraries"></a>Librerie client

[Ottenere le librerie client più recenti](/analysis-services/client-libraries?view=azure-analysis-services-current&preserve-view=true)

Tutte le connessioni a qualunque tipo di server richiedono le librerie client AMO, ADOMD.NET e OLEDB aggiornate per connettersi e interagire con un server di Analysis Services. Per SSMS, Visual Studio, Excel 2016 e versioni successive e Power BI, le librerie client più recenti vengono installate o aggiornate con le versioni mensili. In alcuni casi, tuttavia, è possibile che un'applicazione non abbia la versione più recente. Ad esempio, quando i criteri ritardano gli aggiornamenti o Microsoft 365 aggiornamenti si trovano sul canale posticipato.

> [!NOTE]
> Le librerie client non sono in grado di connettersi a Azure Analysis Services tramite i server proxy che richiedono un nome utente e una password. 

## <a name="server-name"></a>Nome server

Quando si crea un server di Analysis Services in Azure, si specifica un nome univoco e l'area in cui il server deve essere creato. Quando si specifica il nome del server in una connessione, lo schema di denominazione del server è il seguente:

```
<protocol>://<region>/<servername>
```
 Dove protocol è la stringa **asazure**, region è l'Uri in cui è stato creato il server, ad esempio westus.asazure.windows.net, e servername è il nome del server univoco all'interno dell'area.

### <a name="get-the-server-name"></a>Ottenere il nome del server

Nel **portale di Azure** > server > **Panoramica** > **Nome server** copiare l'intero nome del server. Se anche altri utenti nell'organizzazione si connettono a questo server, è opportuno condividere il nome del server. Quando si specifica un nome di server, è necessario usare l'intero percorso.

![Ottenere il nome del server in Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)

> [!NOTE]
> Il protocollo per l'area Stati Uniti orientali 2 è **aspaaseastus2**.

## <a name="connection-string"></a>Stringa di connessione

Quando ci si connette a Azure Analysis Services usando il modello a oggetti tabulare, usare i formati seguenti per la stringa di connessione:

###### <a name="integrated-azure-active-directory-authentication"></a>Autenticazione integrata di Azure Active Directory

L'autenticazione integrata seleziona la cache delle credenziali di Azure Active Directory, se disponibile. In caso contrario, viene visualizzata la finestra di accesso di Azure.

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;"
```


###### <a name="azure-active-directory-authentication-with-username-and-password"></a>Autenticazione di Azure Active Directory con nome utente e password

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;User ID=<user name>;Password=<password>;Persist Security Info=True; Impersonation Level=Impersonate;";
```

###### <a name="windows-authentication-integrated-security"></a>Autenticazione di Windows (sicurezza integrata)

Usare l'account di Windows su cui è in esecuzione il processo corrente.

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>; Integrated Security=SSPI;Persist Security Info=True;"
```

## <a name="connect-using-an-odc-file"></a>Connettersi usando un file con estensione odc

Con le versioni precedenti di Excel, gli utenti possono connettersi a un server di Azure Analysis Services usando un file Office Data Connection, con estensione odc. Per altre informazioni, vedere [Creare un file Office Data Connection (con estensione odc)](analysis-services-odc.md).

## <a name="connect-as-a-linked-server-from-sql-server"></a>Connettersi come server collegato da SQL Server

SQL Server possibile connettersi a una risorsa Azure Analysis Services come [server collegato](/sql/relational-databases/linked-servers/create-linked-servers-sql-server-database-engine) specificando MSOLAP come provider dell'origine dati. Prima di configurare una connessione a un server collegato, assicurarsi di installare la [libreria client di MSOLAP](/analysis-services/client-libraries?view=azure-analysis-services-current&preserve-view=true) più recente (provider). 

Per le connessioni del server collegato a Azure Analysis Services, è necessario creare un'istanza del provider MSOLAP all'esterno del processo di SQL Server. Quando si configurano le opzioni del server collegato, assicurarsi che l'opzione Consenti in- **Process** **non sia selezionata**.

Se è selezionata l'opzione Consenti in- **Process** e viene creata un'istanza del provider nel processo di SQL Server, viene restituito l'errore seguente:

```
OLE DB provider "MSOLAP" for linked server "(null)" returned message "The following system error occurred: ".

OLE DB provider "MSOLAP" for linked server "(null)" returned message "The connection failed because user credentials are needed and Sign-In UI is not allowed.".

Msg 7303, Level 16, State 1, Line 2
Cannot initialize the data source object of OLE DB provider "MSOLAP" for linked server "(null)".
```



## <a name="next-steps"></a>Passaggi successivi

[Connettersi con Excel](analysis-services-connect-excel.md)    
[Connetti con Power BI](analysis-services-connect-pbi.md)   
[Gestire il server](analysis-services-manage.md)