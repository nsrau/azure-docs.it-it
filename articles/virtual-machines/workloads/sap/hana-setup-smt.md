---
title: Come configurare il server SMT per SAP HANA in Azure (istanze Large) | Microsoft Docs
description: Come configurare il server SMT per SAP HANA in Azure (istanze Large).
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 690f41e941f2d1db8fc92d225a54d07570299222
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58313136"
---
# <a name="set-up-smt-server-for-suse-linux"></a>Configurare il server SMT per SUSE Linux
Le istanze Large di SAP HANA non hanno connettività diretta a Internet. La registrazione di un'unità di questo tipo nel provider del sistema operativo e il download e l'applicazione di aggiornamenti non sono processi semplici. Per SUSE Linux una soluzione può essere configurare un server SMT in una macchina virtuale di Azure. Ospitare la macchina virtuale in una rete virtuale di Azure, che è connessa all'istanza Large di HANA. Con un server SMT, l'unità di istanze Large di HANA può eseguire la registrazione e scaricare aggiornamenti. 

Per altre informazioni su SUSE, vedere le [Subscription Management Tool for SLES 12 SP2](https://www.suse.com/documentation/sles-12/pdfdoc/book_smt/book_smt.pdf). 

Prerequisiti per l'installazione di un server SMT che esegua l'attività per istanze Large di HANA sono:

- Una rete virtuale connessa con il circuito ExpressRoute per HANA in istanze Large.
- Un account SUSE associato a un'organizzazione. L'organizzazione deve avere una sottoscrizione SUSE valida.

## <a name="install-smt-server-on-an-azure-virtual-machine"></a>Installare un server SMT in una macchina virtuale di Azure

Prima di tutto è necessario accedere a [SUSE Customer Center](https://scc.suse.com/).

Passare a **Organizzazione** > **Credenziali dell'organizzazione**. In questa sezione sono disponibili le credenziali necessarie per la configurazione del server SMT.

Quindi, installare una macchina virtuale SUSE Linux nella rete virtuale di Azure. Per distribuire la macchina virtuale, acquisire un'immagine di tipo SLES 12 SP2 dalla raccolta di Azure (selezionare l'immagine BYOS SUSE). Durante il processo di distribuzione non definire alcun nome DNS e non usare indirizzi IP statici.

![Screenshot della distribuzione della macchina virtuale per il server SMT](./media/hana-installation/image3_vm_deployment.png)

La macchina virtuale distribuita è più piccola e ha ottenuto l'indirizzo IP interno nella rete virtuale di Azure di 10.34.1.4. Il nome della macchina virtuale è *smtserver*. Dopo l'installazione viene verificata la connettività alle unità di istanze Large di HANA. In base al modo in cui è stata organizzata la risoluzione dei nomi, potrebbe essere necessario configurare la risoluzione delle unità di istanze Large di HANA etc/hosts della macchina virtuale di Azure. 

Aggiungere un disco alla macchina virtuale. Questo disco viene utilizzato per contenere gli aggiornamenti e il disco di avvio stesso potrebbe essere troppo piccolo. Nel caso specifico, il disco è stato montato in /srv/www/htdocs, come mostrato nello screenshot seguente. Dovrebbe essere sufficiente un disco da 100 GB.

![Screenshot della distribuzione della macchina virtuale per il server SMT](./media/hana-installation/image4_additional_disk_on_smtserver.PNG)

Accedere alle unità di istanze Large di HANA, rimanere in /etc/hosts e verificare se è possibile raggiungere la macchina virtuale di Azure che dovrebbe eseguire il server SMT in rete.

Dopo aver verificato questo aspetto, accedere alla macchina virtuale di Azure per eseguire il server SMT. Se si usa putty per accedere alla macchina virtuale, eseguire questa sequenza di comandi nella finestra di bash:

```
cd ~
echo "export NCURSES_NO_UTF8_ACS=1" >> .bashrc
```

Riavviare il bash utilizzato per attivare le impostazioni. Avviare quindi YAST.

Connettere la macchina virtuale (smtserver) al sito di SUSE.

```
smtserver:~ # SUSEConnect -r <registration code> -e s<email address> --url https://scc.suse.com
Registered SLES_SAP 12.2 x86_64
To server: https://scc.suse.com
Using E-Mail: email address
Successfully registered system.
```

Una volta che la macchina virtuale è connessa al sito di SUSE, installare i pacchetti smt. Digitare il seguente comando putty per installare i pacchetti smt:

```
smtserver:~ # zypper in smt
Refreshing service 'SUSE_Linux_Enterprise_Server_for_SAP_Applications_12_SP2_x86_64'.
Loading repository data...
Reading installed packages...
Resolving package dependencies...
```


È anche possibile usare lo strumento YAST per installare i pacchetti smt. In YAST passare a **Software Maintenance** (Manutenzione software) e cercare smt. Selezionare **smt**, che viene impostato automaticamente su yast2-smt.

![Screenshot di SMT in YAST](./media/hana-installation/image5_smt_in_yast.PNG)


Accettare la selezione per l'installazione su smtserver. Al termine dell'installazione, passare alla configurazione del server SMT. Immettere le credenziali dell'organizzazione recuperate in precedenza da SUSE Customer Center. Immettere anche il nome host della macchina virtuale di Azure come URL del server SMT. In questa dimostrazione, le https:\//smtserver.

![Screenshot della configurazione del server SMT](./media/hana-installation/image6_configuration_of_smtserver1.png)

Testare ora il funzionamento della connessione a SUSE Customer Center. Come illustrato nello screenshot seguente, nel caso della dimostrazione non sono stati rilevati problemi di funzionamento.

![Screenshot di test della connessione a SUSE Customer Center](./media/hana-installation/image7_test_connect.png)

Dopo l'avvio del programma di installazione di SMT, specificare una password del database. Poiché si tratta di una nuova installazione, è necessario definire tale password come illustrato nello screenshot seguente.

![Screenshot della definizione di password per il database](./media/hana-installation/image8_define_db_passwd.PNG)

Il passaggio successivo consiste nella creazione di un certificato.

![Screenshot della creazione di un certificato per il server SMT](./media/hana-installation/image9_certificate_creation.PNG)

Al termine della configurazione, potrebbero essere necessari alcuni minuti per eseguire il controllo di sincronizzazione. Dopo l'installazione e la configurazione del server SMT, il repository della directory è disponibile nel punto di montaggio /srv/www/htdocs/. Il repository dovrebbe includere anche alcune sottodirectory. 

Riavviare il server SMT e i servizi correlati con questi comandi.

```
rcsmt restart
systemctl restart smt.service
systemctl restart apache2
```

## <a name="download-packages-onto-smt-server"></a>Scaricare pacchetti nel server SMT

Dopo il riavvio di tutti i servizi, selezionare i pacchetti appropriati in SMT Management tramite Yast. La selezione dei pacchetti dipende dall'immagine del sistema operativo del server di istanze Large di HANA. La selezione dei pacchetti non dipende dalla versione di SLES o dalla versione della macchina virtuale che esegue il server SMT. Lo screenshot seguente illustra un esempio della schermata di selezione.

![Schermata di selezione dei pacchetti](./media/hana-installation/image10_select_packages.PNG)

Successivamente, avviare la copia iniziale dei pacchetti selezionati sul server SMT configurato. L'operazione di copia viene attivata nella shell tramite il comando smt-mirror.

![Screenshot del download dei pacchetti nel server SMT](./media/hana-installation/image11_download_packages.PNG)

I pacchetti dovrebbero essere copiati nelle directory create nel punto di montaggio /srv/www/htdocs. Questo processo può richiedere un'ora o più tempo, a seconda del numero di pacchetti selezionati. Al termine del processo, è necessario passare all'installazione del client SMT. 

## <a name="set-up-the-smt-client-on-hana-large-instance-units"></a>Configurare il client SMT in unità di istanze Large di HANA

Il o i client in questo caso sono le unità di istanze Large di HANA. Il programma di installazione del server SMT ha copiato lo script clientSetup4SMT.sh nella macchina virtuale di Azure. Copiare tale script nell'unità di istanze Large di HANA da connettere al server SMT. Avviare lo script con l'opzione -h e assegnare allo script come parametro il nome del server SMT. In questo esempio il nome è *smtserver*.

![Screenshot della configurazione del client SMT](./media/hana-installation/image12_configure_client.PNG)

È possibile che il caricamento del certificato dal server da parte del client abbia esito positivo, ma la registrazione abbia esito negativo, come mostrato nel seguente screenshot.

![Screenshot dell'errore di registrazione del client](./media/hana-installation/image13_registration_failed.PNG)

In caso di esito negativo della registrazione, vedere il [documento del supporto tecnico SUSE](https://www.suse.com/de-de/support/kb/doc/?id=7006024) e seguire la procedura indicata nel documento.

> [!IMPORTANT] 
> Come nome del server, specificare il nome della macchina virtuale (in questo caso *smtserver*), senza il nome di dominio completo. 

Dopo aver eseguito questi passaggi, eseguire il comando seguente nell'unità di istanze Large di HANA:

```
SUSEConnect –cleanup
```

> [!Note] 
> Attendere qualche minuto dopo questo passaggio. Se si esegue immediatamente clientSetup4SMT.sh, si potrebbe ottenere un errore.

Se si incontra un problema che è necessario risolvere tramite la procedura illustrata nell'articolo relativo a SUSE, sarà necessario riavviare clientSetup4SMT.sh nell'unità di istanze Large di HANA. L'operazione dovrebbe essere completata correttamente.

![Screenshot dell'operazione di registrazione riuscita del client](./media/hana-installation/image14_finish_client_config.PNG)

È stato configurato il client SMT dell'unità di istanze Large di HANA per la connessione al server SMT installato nella macchina virtuale di Azure. È ora possibile usare i comandi "zypper up" o "zypper in" per installare gli aggiornamenti del sistema operativo nelle istanze Large di HANA oppure installare pacchetti aggiuntivi. È possibile ottenere solo gli aggiornamenti che sono stati scaricati in precedenza nel server SMT.

## <a name="next-steps"></a>Passaggi successivi
- [Installazione di HANA in HLI](hana-example-installation.md).











