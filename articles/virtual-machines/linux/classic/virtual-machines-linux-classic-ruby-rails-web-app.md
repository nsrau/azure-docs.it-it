---
title: Ospitare un sito Web Rails in una macchina virtuale di Linux su Ruby | Documentazione Microsoft
description: Impostare e ospitare un sito Web basato su Ruby on Rails in Azure usando una macchina virtuale Linux.
services: virtual-machines-linux
documentationcenter: ruby
author: rmcmurray
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: aad32685-3550-4bff-9c73-beb8d70b3291
ms.service: virtual-machines-linux
ms.workload: web
ms.tgt_pltfrm: vm-linux
ms.devlang: ruby
ms.topic: article
ms.date: 06/27/2017
ms.author: robmcm
ms.openlocfilehash: 0518519da6c5e62a863a47d6743ab7b7c5923acf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="ruby-on-rails-web-application-on-an-azure-vm"></a>Applicazione Web Ruby on Rails in una macchina virtuale di Azure
Questa esercitazione mostra come ospitare un sito Web Ruby on Rails usando una macchina virtuale Linux.  

Questa esercitazione è stata convalidata usando Ubuntu Server 14.04 LTS. Se si usa una distribuzione Linux diversa, è necessario modificare la procedura per installare Rails.

> [!IMPORTANT]
> Azure offre due modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../../../azure-resource-manager/resource-manager-deployment-model.md).  Questo articolo illustra l'uso del modello di distribuzione classica. Microsoft consiglia di usare il modello di Gestione risorse per le distribuzioni più recenti.
>
>

## <a name="create-an-azure-vm"></a>Creare una macchina virtuale di Azure
Iniziare creando una macchina virtuale di Azure con un'immagine Linux.

Per creare la VM, è possibile usare il portale di Azure o l'interfaccia della riga di comando (CLI) di Azure.

### <a name="azure-portal"></a>Portale di Azure
1. Accedere al [portale di Azure](https://portal.azure.com)
2. Fare clic su **Nuovo**, quindi digitare "Ubuntu Server 14.04" nella casella di ricerca. Fare clic sulla voce restituita dalla ricerca. Come modello di distribuzione selezionare **Classico**, quindi fare clic su "Crea".
3. Nel pannello Informazioni di base immettere i valori per i campi obbligatori: nome (per la VM), nome utente, tipo di autenticazione e le credenziali corrispondenti, sottoscrizione di Azure, gruppo di risorse e località.

   ![Creare una nuova immagine Ubuntu](./media/virtual-machines-linux-classic-ruby-rails-web-app/createvm.png)

4. Dopo il provisioning della macchina virtuale, fare clic sul nome della macchina virtuale e quindi fare clic su **Endpoint** nella categoria **Impostazioni**. Trovare l'endpoint SSH, elencato in **Autonomo**.

   ![Endpoint predefinito](./media/virtual-machines-linux-classic-ruby-rails-web-app/endpointsnewportal.png)

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure
Seguire i passaggi in [Creazione rapida di una macchina virtuale che esegue Linux][vm-instructions].

Al termine del provisioning della macchina virtuale, è possibile ottenere l'endpoint SSH eseguendo il comando seguente:

    azure vm endpoint list <vm-name>  

## <a name="install-ruby-on-rails"></a>Installare Ruby on Rails
1. Usare SSH per connettersi alla macchina virtuale.
2. Dalla sessione SSH usare i comandi seguenti per installare Ruby nella macchina virtuale:

        sudo apt-get update -y
        sudo apt-get upgrade -y

        sudo apt-add-repository ppa:brightbox/ruby-ng
        sudo apt-get update
        sudo apt-get install ruby2.4

        > [!TIP]
        > The brightbox repository contains the current Ruby distribution.

    L'installazione potrebbe richiedere alcuni minuti. Al termine, immettere il comando seguente per verificare se l'installazione di Ruby è stata completata:

        ruby -v

3. Immettere il comando seguente per installare Rails:

        sudo gem install rails --no-rdoc --no-ri -V

    Per velocizzare l'operazione, usare i flag --no-rdoc e --no-ri per ignorare l'installazione della documentazione.
    È probabile che l'esecuzione di questo comando richieda tempo, quindi l'aggiunta di -V consentirà di visualizzare lo stato dell'installazione.

## <a name="create-and-run-an-app"></a>Creare ed eseguire un'applicazione
Mentre si è ancora connessi con SSH, eseguire i comandi seguenti:

    rails new myapp
    cd myapp
    rails server -b 0.0.0.0 -p 3000

Il comando [new](http://guides.rubyonrails.org/command_line.html#rails-new) crea una nuova app Rails. Il comando [server](http://guides.rubyonrails.org/command_line.html#rails-server) avvia il server Web WEBrick fornito con Rails. (per l'uso in un ambiente di produzione sarà opportuno usare un server differente, ad esempio Unicorn o Passenger).

L'output dovrebbe essere simile al seguente.

    => Booting WEBrick
    => Rails 4.2.1 application starting in development on http://0.0.0.0:3000
    => Run `rails server -h` for more startup options
    => Ctrl-C to shutdown server
    [2015-06-09 23:34:23] INFO  WEBrick 1.3.1
    [2015-06-09 23:34:23] INFO  ruby 1.9.3 (2013-11-22) [x86_64-linux]
    [2015-06-09 23:34:23] INFO  WEBrick::HTTPServer#start: pid=27766 port=3000

## <a name="add-an-endpoint"></a>Aggiungere un endpoint
1. Andare al [portale di Azure][https://portal.azure.com] e selezionare la VM.

2. Selezionare **ENDPOINT** in **Impostazioni** sul lato sinistro della pagina.

3. Nella parte superiore della pagina fare clic su **AGGIUNGI**.

4. Nella finestra di dialogo **Aggiungi endpoint** immettere le informazioni seguenti:

   * **Nome**: HTTP
   * **Protocollo**: TCP
   * **Porta pubblica**: 80
   * **Porta privata**: 3000
   * **Indirizzo IP mobile**: Disabilitato
   * **Elenco di controllo di accesso - Ordine**: 1001 o un altro valore che imposta la priorità di questa regola di accesso.
   * **Elenco di controllo di accesso - Nome**: allowHTTP
   * **Elenco di controllo di accesso - Azione**: consenti
   * **Elenco di controllo di accesso - Subnet remota**: 1.0.0.0/16

     Questo endpoint ha una porta pubblica 80 che instraderà il traffico alla porta privata 3000, dove è in ascolto il server Rails. La regola dell'elenco di controllo di accesso consente il traffico pubblico sulla porta 80.

     ![new-endpoint](./media/virtual-machines-linux-classic-ruby-rails-web-app/createendpoint.png)

5. Fare clic su OK per salvare l'endpoint.

6. Verrà visualizzato il messaggio **Salvataggio dell'endpoint della macchina virtuale**. Quando questo messaggio scompare, significa che l'endpoint è attivo. A questo punto è possibile testare l'applicazione passando al nome DNS della macchina virtuale. L'aspetto del sito Web dovrebbe essere simile al seguente:

    ![Pagina predefinita Rails][default-rails-cloud]

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione è stata eseguita la maggior parte dei passaggi manualmente. In un ambiente di produzione, si potrebbe scrivere l'applicazione in un computer di sviluppo e distribuirla nella macchina virtuale di Azure. Inoltre, la maggior parte degli ambienti di produzione ospita l'applicazione Rails insieme a un altro processo server, ad esempio Apache o NginX, che gestisce il routing delle richieste a più istanze dell'applicazione Rails e la distribuzione di risorse statiche. Per altre informazioni, vedere http://rubyonrails.org/deploy/.

Per altre informazioni su Ruby on Rails, vedere le [Guide di Ruby on Rails][rails-guides].

Per usare servizi di Azure dall'applicazione Ruby, vedere:

* [Archiviare dati non strutturati mediante BLOB][blobs]
* [Archiviare coppie chiave-valore mediante tabelle][tables]
* [Distribuire contenuti ad ampia larghezza di banda con la rete per la distribuzione di contenuti][cdn-howto]

<!-- WA.com links -->
[blobs]:../../../storage/blobs/storage-ruby-how-to-use-blob-storage.md
[cdn-howto]:https://azure.microsoft.com/develop/ruby/app-services/
[tables]:../../../cosmos-db/table-storage-how-to-use-ruby.md
[vm-instructions]:createportal.md

<!-- External Links -->
[rails-guides]:http://guides.rubyonrails.org/
[sqlite3]:http://www.sqlite.org/

<!-- Images -->

[default-rails-cloud]:./media/virtual-machines-linux-classic-ruby-rails-web-app/basicrailscloud.png
[vmlist]:./media/virtual-machines-linux-classic-ruby-rails-web-app/vmlist.png
[endpoints]:./media/virtual-machines-linux-classic-ruby-rails-web-app/endpoints.png
[new-endpoint]:./media/virtual-machines-linux-classic-ruby-rails-web-app/newendpoint.png
[new-endpoint1]:./media/virtual-machines-linux-classic-ruby-rails-web-app/newendpoint1.png
