---
title: Gestire le autorizzazioni utente a livello di file e cartella - Azure HDInsight | Microsoft Doc
description: Come gestire le autorizzazioni su file e cartelle per i cluster HDInsight aggiunti a un dominio.
services: hdinsight
documentationcenter: 
tags: azure-portal
author: maxluk
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: maxluk
ms.openlocfilehash: 42d617ffeb8c2fee6be6d747b39d80b09774a1c3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-user-permissions-at-the-file-and-folder-levels"></a>Gestire le autorizzazioni utente a livello di file e di cartella

I [cluster HDInsight aggiunti al dominio](hdinsight-domain-joined-introduction.md) usano l'autenticazione avanzata con gli utenti di Azure Active Directory (Azure AD) e criteri di *controllo degli accessi in base al ruolo* per numerosi servizi, ad esempio YARN e Hive. Se l'archivio dati predefinito per il cluster è Archiviazione di Azure o BLOB del servizio di archiviazione di Azure, è possibile applicare anche autorizzazioni a livello di file e cartella. È possibile usare Apache Ranger per controllare l'accesso ai file del cluster per gli utenti e i gruppi di Azure AD sincronizzati.
<!-- [synchronized Azure AD users and groups](hdinsight-sync-aad-users-to-cluster.md). -->

Nell'istanza di Apache Ranger per i cluster HDInsight aggiunti al dominio è preconfigurato il servizio Ranger-WASB. Tale servizio è un motore di gestione di criteri analogo a Ranger-HDFS, ma con una diversa applicazione dei criteri di accesso a Ranger. Nel servizio Ranger-WASB, se a una richiesta di risorse in ingresso non corrispondono criteri Ranger, la risposta predefinita è DENY. Il servizio Ranger non trasmette il controllo autorizzazioni a BLOB del servizio di archiviazione di Azure.

## <a name="permission-and-policy-model"></a>Modello di criteri e autorizzazioni

Le richieste di accesso alle risorse vengono valutate usando il flusso seguente:

![Flusso di valutazione dei criteri di Apache Ranger](./media/hdinsight-add-acls-at-file-folder-levels/ranger-policy-evaluation-flow.png)

Le regole DENY vengono valutate per prime, seguite dalle regole ALLOW. Al termine della verifica di corrispondenza, se non è soddisfatto alcun criterio viene restituito un valore DENY.

### <a name="user-variable"></a>Variabile USER

È possibile usare la variabile `{USER}` quando si assegnano i criteri a un utente per accedere a una sottodirectory `/{username}`, ad esempio:

```
resource: path=/app-logs/{USER}, user: {USER}, recursive=true, permissions: all, delegateAdmin=true
```

I criteri precedenti concedono agli utenti l'autorizzazione ad accedere alla propria sottocartella nella directory `/app-logs/`. L'aspetto di tali criteri nell'interfaccia utente di Ranger è il seguente:

![Esempio dell'uso della variabile USER](./media/hdinsight-add-acls-at-file-folder-levels/user-variable.png)

### <a name="policy-model-examples"></a>Esempi di modello dei criteri

La tabella seguente elenca alcuni esempi di funzionamento del modello di criteri:

| Criteri Ranger | File system esistente | Richiesta dell'utente | Risultato |
| -- | -- | -- | -- |
| /data/finance/, bob, WRITE | /data | bob, Create file /data/finance/mydatafile.txt | ALLOW - Viene creata la cartella intermedia finance a causa del controllo predecessore |
| /data/finance/, bob, WRITE | /data | alice, Create file /data/finance/mydatafile.txt | DENY - Nessun criterio di corrispondenza |
| /data/finance*, bob, WRITE | /data | bob, Create file /data/finance/mydatafile.txt | ALLOW - In questo caso è presente il criterio ricorsivo facoltativo (`*`). Vedere la sezione [Caratteri jolly](#wildcards) |
| /data/finance/mydatafile.txt, bob, WRITE | /data | bob, Create file /data/finance/mydatafile.txt | DENY - Il controllo predecessore su /data ha esito negativo perché non sono presenti criteri |
| /data/finance/mydatafile.txt, bob, WRITE | /data/finance | bob, Create file /data/finance/mydatafile.txt | DENY - Nessun criterio per il controllo predecessore su /data/finance |

Sono necessarie autorizzazioni a livello di cartella o di file, in base al tipo di operazione. Una chiamata "read/open", ad esempio, richiede l'accesso in lettura a livello di file, mentre una chiamata "create" richiede autorizzazioni al livello della cartella predecessore.

### <a name="wildcards-"></a>Caratteri jolly (*)

Quando un carattere jolly (`*`) è presente nel percorso di un criterio, il carattere si applica a tale percorso e all'intero sottoalbero relativo. Questo tipo di ricorsione equivale all'uso di un elemento `recurse-flag`. In Ranger-WASB il carattere jolly indica sia la ricorsione che una corrispondenza di nome parziale.

## <a name="manage-file-and-folder-level-permissions-with-apache-ranger"></a>Gestire autorizzazioni a livello di file e cartella con Apache Ranger

Se non è già stato fatto, seguire [queste istruzioni](hdinsight-domain-joined-configure.md) per effettuare il provisioning di un nuovo cluster aggiunto al dominio.

Spostarsi su `https://<YOUR CLUSTER NAME>.azurehdinsight.net/ranger/` per aprire Ranger-WASB. Immettere il nome utente e la password di amministratore del cluster definiti durante la creazione del cluster.

Dopo aver eseguito l'accesso, viene visualizzato il dashboard di Ranger:

![Dashboard di Ranger](./media/hdinsight-add-acls-at-file-folder-levels/ranger-dashboard.png)

Per visualizzare le autorizzazioni correnti su file e cartelle per l'account di Archiviazione di Azure associato del cluster, fare clic sul collegamento ***CLUSTERNAME*_wasb** nella casella di controllo WASB.

![Dashboard di Ranger](./media/hdinsight-add-acls-at-file-folder-levels/wasb-dashboard-link.png)

Viene visualizzato l'elenco dei criteri correnti. Come punto iniziale, sono inclusi diversi criteri tipici. Verificare i dettagli dei criteri per visualizzarne gli esempi di uso.

Per ogni criterio, è possibile visualizzare se è abilitato e se è configurata la registrazione di controllo, nonché visualizzare tutti gli utenti e gruppi assegnati. Sono presenti due pulsanti di azione per ogni criterio, ovvero Edit (Modifica) e Delete (Elimina).

![Elenco di criteri](./media/hdinsight-add-acls-at-file-folder-levels/policy-list.png)

### <a name="adding-a-new-policy"></a>Aggiunta di un nuovo criterio

1. Nella parte superiore destra della pagina dei criteri WASB, selezionare **Add New Policy** (Aggiungi nuovo criterio).

    ![Aggiungere nuovi criteri](./media/hdinsight-add-acls-at-file-folder-levels/add-new.png)

2. In **Policy Name** (Nome criterio) immettere un nome descrittivo. Compilare il campo **Storage Account** (Account archiviazione) con l'account di archiviazione di Azure per il proprio cluster (*NOME_ACCOUNT*.blob.core.windows.net) e il campo **Storage Account Container** (Contenitore account archiviazione) con il nome del contenitore di account di archiviazione specificato quando è stato creato il cluster. Immettere un valore nel campo **Relative Path** (Percorso relativo) in cui il percorso è relativo al cluster per la cartella o il file a cui è stato eseguito l'accesso.

    ![Nuovo modulo dei criteri](./media/hdinsight-add-acls-at-file-folder-levels/new-policy.png)

3. Sotto il modulo specificare un valore per **Allow Conditions** (Consenti condizioni) per questa nuova risorsa. Selezionare utenti e gruppi applicabili e impostare le relative autorizzazioni. Nell'esempio seguente tutti gli utenti del gruppo `sales` dispongono dell'accesso in lettura/scrittura.

    ![Condizioni consentite per gli utenti del gruppo vendite](./media/hdinsight-add-acls-at-file-folder-levels/allow-sales.png)

4. Selezionare **Salva**.

### <a name="example-policy-conditions"></a>Condizioni di criteri di esempio

Il [flusso di valutazione dei criteri](#permission-and-policy-model) di Apache Ranger consente di specificare qualsiasi combinazione di condizione consentita o negata in grado di soddisfare le esigenze dell'utente. Di seguito sono disponibili alcuni esempi:

1. Condizioni consentite a tutti gli utenti del gruppo vendite, ma non agli utenti interni:

    ![Condizioni consentite agli utenti del gruppo vendite, ma non agli utenti interni](./media/hdinsight-add-acls-at-file-folder-levels/allow-sales-deny-interns.png)

2. Condizioni consentite a tutti gli utenti del gruppo vendite e negate a tutti gli utenti interni, ad eccezione di un utente interno denominato "hiveuser3", che deve disporre dell'accesso in lettura:

    ![Condizioni consentite per gli utenti del gruppo vendite e negate per quelli interni, ad eccezione di hiveuser3](./media/hdinsight-add-acls-at-file-folder-levels/allow-sales-deny-interns-except-hiveuser3.png)

## <a name="next-steps"></a>Passaggi successivi

* [Configurare criteri Hive in HDInsight aggiunto al dominio](hdinsight-domain-joined-run-hive.md)
* [Gestione dei cluster HDInsight aggiunti al dominio](hdinsight-domain-joined-manage.md)
* [Autorizzare gli utenti per le viste di Ambari](hdinsight-authorize-users-to-ambari.md)

<!-- * [Synchronize Azure AD users and groups](hdinsight-sync-aad-users-to-cluster.md) -->

