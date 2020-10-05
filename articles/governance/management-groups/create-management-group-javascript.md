---
title: 'Avvio rapido: Creare un gruppo di gestione con JavaScript'
description: In questo argomento di avvio rapido si usa JavaScript per creare un gruppo di gestione con cui organizzare le risorse in una gerarchia.
ms.date: 09/30/2020
ms.topic: quickstart
ms.custom: devx-track-javascript
ms.openlocfilehash: 7a234a6619eafd650451ae5d6bce37388c824f33
ms.sourcegitcommit: ffa7a269177ea3c9dcefd1dea18ccb6a87c03b70
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/30/2020
ms.locfileid: "91604555"
---
# <a name="quickstart-create-a-management-group-with-javascript"></a>Avvio rapido: Creare un gruppo di gestione con JavaScript

I gruppi di gestione sono contenitori che semplificano la gestione dell'accesso, dei criteri e della conformità tra più sottoscrizioni. Creare questi contenitori per creare una gerarchia efficiente ed efficace utilizzabile con [Criteri di Azure](../policy/overview.md) e [Controllo degli accessi in base al ruolo di Azure](../../role-based-access-control/overview.md). Per altre informazioni sui gruppi di gestione, vedere [Organizzare le risorse con i gruppi di gestione di Azure](overview.md).

La creazione del primo gruppo di gestione nella directory può richiedere fino a 15 minuti. La prima volta vengono eseguiti processi che eseguono la configurazione del servizio dei gruppi di gestione all'interno di Azure per la directory. Al termine dei processi, si riceve una notifica. Per altre informazioni, vedere [Configurazione iniziale dei gruppi di gestione](./overview.md#initial-setup-of-management-groups).

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

- Prima di iniziare verificare che sia installata almeno la versione 12 di [Node.js](https://nodejs.org/).

- Se la [protezione della gerarchia](./how-to/protect-resource-hierarchy.md#setting---require-authorization) non è abilitata, qualsiasi utente di Azure AD nel tenant può creare un gruppo di gestione senza avere autorizzazioni di scrittura assegnate per tale gruppo di gestione. Questo nuovo gruppo di gestione diventa un elemento figlio del gruppo di gestione radice o del [gruppo di gestione predefinito](./how-to/protect-resource-hierarchy.md#setting---default-management-group) e all'autore viene assegnato il ruolo "Proprietario". Il servizio del gruppo di gestione offre questa possibilità per evitare che siano necessarie assegnazioni di ruolo a livello di radice. Nessun utente ha accesso al gruppo di gestione radice quando viene creato. Per evitare il problema di reperire gli amministratori globali di Azure AD per iniziare a usare i gruppi di gestione, è consentita la creazione dei gruppi di gestione iniziali al livello radice.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="application-setup"></a>Configurazione dell'applicazione

Per consentire a JavaScript di eseguire query su Azure Resource Graph, è necessario configurare l'ambiente. Questa configurazione funziona ovunque sia possibile usare JavaScript, incluso [Bash in Windows 10](/windows/wsl/install-win10).

1. Configurare un nuovo progetto Node.js usando il comando seguente.

   ```bash
   npm init -y
   ```

1. Aggiungere un riferimento al modulo yargs.

   ```bash
   npm install yargs
   ```

1. Aggiungere un riferimento al modulo Azure Resource Graph.

   ```bash
   npm install @azure/arm-managementgroups
   ```

1. Aggiungere un riferimento alla libreria di autenticazione di Azure.

   ```bash
   npm install @azure/ms-rest-nodeauth
   ```

   > [!NOTE]
   > Verificare in _package.json_ che la versione di `@azure/arm-managementgroups` sia **1.1.0** o successive e che la versione di `@azure/ms-rest-nodeauth` sia **3.0.5** o successive.

## <a name="create-the-management-group"></a>Creare il gruppo di gestione

1. Creare un nuovo file denominato _index.js_ e immettere il codice seguente.

   ```javascript
   const argv = require("yargs").argv;
   const authenticator = require("@azure/ms-rest-nodeauth");
   const managementGroups = require("@azure/arm-managementgroups");

   if (argv.groupID && argv.displayName) {
       const createMG = async () => {
          const credentials = await authenticator.interactiveLogin();
          const client = new managementGroups.ManagementGroupsAPI(credentials);
          const result = await client.createOrUpdate(
             groupId: argv.groupID,
             {
                 displayName: argv.displayName
             }
          );
          console.log(result);
       };

       createMG();
   }
   ```

1. Immettere il comando seguente nel terminale:

   ```bash
   node index.js --groupID "<NEW_MG_GROUP_ID>" --displayName "<NEW_MG_FRIENDLY_NAME>"
   ```

   Assicurarsi di sostituire ogni segnaposto `<>` del token rispettivamente con l'_ID del gruppo di gestione_ e il _nome descrittivo del gruppo di gestione_.

   Quando lo script tenta di eseguire l'autenticazione, nel terminale verrà visualizzato un messaggio simile al seguente:

   > Per accedere, usare un Web browser per aprire la pagina https://microsoft.com/devicelogin e immettere il codice FGB56WJUGK per eseguire l'autenticazione.

   Dopo l'autenticazione nel browser, l'esecuzione dello script continua.

Il risultato della creazione del gruppo di gestione viene restituito alla console.

## <a name="clean-up-resources"></a>Pulire le risorse

Per rimuovere le librerie installate dall'applicazione, eseguire il comando seguente.

```bash
npm uninstall @azure/arm-managementgroups @azure/ms-rest-nodeauth yargs
```

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stato creato un gruppo di gestione per organizzare la gerarchia delle risorse. Il gruppo di gestione può contenere sottoscrizioni o altri gruppi di gestione.

Per altre informazioni sui gruppi di gestione e su come gestire la gerarchia delle risorse, continuare con:

> [!div class="nextstepaction"]
> [Gestire le risorse con i gruppi di gestione](./manage.md)