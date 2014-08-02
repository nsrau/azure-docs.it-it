<properties linkid="manage-linux-common-tasks-user-root-privileges" urlDisplayName="Use root privileges" pageTitle="Use root privileges on Linux virtual machines in Azure" metaKeywords="" description="Learn how to use root privileges on a Linux virtual machine in Azure." metaCanonical="" services="virtual-machines" documentationCenter="" title="Using root privileges on Linux virtual machines in Azure" authors="" solutions="" manager="" editor="" />

Utilizzo di privilegi root su Linux in Macchine virtuali di Azure
=================================================================

Gli utenti possono eseguire comandi con privilegi elevati in una macchina virtuale Linux utilizzando il comando `sudo`. L'esperienza può tuttavia variare in base alla modalità utilizzata per il provisioning del sistema.

1.  **Chiave SSH e password o solo password**: il provisioning della macchina virtuale è stato effettuato con un certificato (file `CER`) e con una password oppure solo con un nome utente e una password. In questo caso, `sudo` richiederà la password dell'utente prima di eseguire il comando.

2.  **Solo chiave SSH**: il provisioning della macchina virtuale è stato effettuato con un certificato (file `CER` o `PEM`), ma senza password. In questo caso, `sudo` **non** richiederà la password dell'utente prima di eseguire il comando.

Chiave SSH e password o solo password
-------------------------------------

Accedere alla macchina virtuale Linux utilizzando l'autenticazione con chiave SSH o password, quindi eseguire i comandi tramite `sudo`, ad esempio:

    # sudo <command>
    [sudo] password for azureuser:

In questo caso, all'utente verrà richiesta una password. Dopo avere immesso la password `sudo` eseguirà il comando con privilegi `root`.

Solo chiave SSH
---------------

Accedere alla macchina virtuale Linux utilizzando l'autenticazione con chiave SSH, quindi eseguire i comandi tramite `sudo`, ad esempio:

    # sudo <command>

In questo caso, all'utente **non** verrà richiesta una password. Dopo avere digitato `<enter>`, `sudo` eseguirà i comandi con privilegi `root`.

