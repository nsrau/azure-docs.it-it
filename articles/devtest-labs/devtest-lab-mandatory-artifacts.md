---
title: Specificare artefatti obbligatori per Azure DevTest Labs | Microsoft Docs
description: Informazioni su come specificare artefatti obbligatori da installare prima di installare gli artefatti selezionati dall'utente sulle macchine virtuali (VM) nel lab.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 0677f8bea35cb34735fdcf34e717eea349fad8bf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85480304"
---
# <a name="specify-mandatory-artifacts-for-your-lab-in-azure-devtest-labs"></a>Specificare artefatti obbligatori per il lab in Azure DevTest Labs
In qualità di proprietario di un Lab, è possibile specificare elementi obbligatori che vengono applicati a ogni computer creato nel Lab. Si pensi a uno scenario in cui si vuole che ogni computer del lab sia connesso alla rete aziendale. In questo caso, ogni utente del lab dovrà aggiungere un artefatto di aggiunta a un dominio durante la creazione della macchina virtuale per assicurarsi che il computer sia connesso al dominio aziendale. In altre parole, gli utenti del lab dovranno essenzialmente ricreare un computer qualora dimenticassero di applicare gli artefatti obbligatori nel computer. Il proprietario di un lab imposta l'artefatto di aggiunta a un dominio come artefatto obbligatorio nel lab. Questo passaggio assicura che ogni computer sia connesso alla rete aziendale, facendo risparmiare tempo e fatica agli utenti del lab.
 
Altri artefatti obbligatori potrebbero includere uno strumento comune usato dal team o un Security Pack correlato alla piattaforma che ogni computer deve avere per impostazione predefinita e così via. In breve, qualsiasi software comune che ogni computer nel lab deve avere diventa un elemento obbligatorio. Se si crea un'immagine personalizzata da un computer a cui sono applicati artefatti obbligatori e quindi si crea un computer nuovo da tale immagine, gli artefatti obbligatori vengono riapplicati nel computer durante la creazione. Questo comportamento implica inoltre che, anche se l'immagine personalizzata non è recente, ogni volta che si crea da essa un computer, durante il flusso di creazione viene applicata la versione più aggiornata degli artefatti obbligatori. 
 
Solo gli artefatti senza parametri sono supportati come obbligatori. L'utente del lab non deve immettere altri parametri durante la creazione del lab, semplificando così il processo di creazione della VM. 

## <a name="specify-mandatory-artifacts"></a>Specificare artefatti obbligatori
È possibile selezionare separatamente gli artefatti obbligatori per i computer Windows e Linux. È anche possibile riordinare questi artefatti in base all'ordine in cui li si vuole applicare. 

1. Nella home page del lab selezionare **Configurazione e criteri** in **IMPOSTAZIONI**. 
3. Selezionare **Artefatti obbligatori** in **RISORSE ESTERNE**. 
4. Selezionare **Modifica** nella sezione **Windows** o nella sezione **Linux**. Questo esempio usa l'opzione **Windows**. 

    ![Pagina Artefatti obbligatori - Pulsante Modifica](media/devtest-lab-mandatory-artifacts/mandatory-artifacts-edit-button.png)
4. Selezionare un artefatto. Questo esempio usa l'opzione **7-Zip**. 
5. Nella pagina **Aggiungi artefatto** selezionare **Aggiungi**. 

    ![Pagina Artefatti obbligatori - Aggiungere 7-zip](media/devtest-lab-mandatory-artifacts/add-seven-zip.png)
6. Per aggiungere un altro artefatto, selezionare l'articolo e selezionare **Aggiungi**. Questo esempio aggiunge **Chrome** come secondo artefatto obbligatorio.

    ![Pagina Artefatti obbligatori - Aggiungere Chrome](media/devtest-lab-mandatory-artifacts/add-chrome.png)
7. Nella pagina **Artefatti obbligatori** viene visualizzato un messaggio che specifica il numero di artefatti selezionati. Se si fa clic sul messaggio, vengono visualizzati gli artefatti selezionati. Selezionare **Salva** per salvare. 

    ![Pagina Artefatti obbligatori - Salvare gli artefatti](media/devtest-lab-mandatory-artifacts/save-artifacts.png)
8. Ripetere i passaggi per specificare gli artefatti obbligatori per le VM Linux. 
    
    ![Pagina Artefatti obbligatori - Artefatti Windows e Linux](media/devtest-lab-mandatory-artifacts/windows-linux-artifacts.png)
9. Per **eliminare** un artefatto dall'elenco, selezionare **... (puntini di sospensione)** alla fine della riga e selezionare **Elimina**. 
10. Per **riordinare** gli artefatti nell'elenco, passare il puntatore del mouse sull'artefatto, selezionare **... (puntini di sospensione)** all'inizio della riga e trascinare l'elemento nella nuova posizione. 
11. Per salvare gli artefatti obbligatori nel lab, selezionare **Salva**. 

    ![Pagina Artefatti obbligatori - Salvare gli artefatti nel lab](media/devtest-lab-mandatory-artifacts/save-to-lab.png)
12. Chiudere la pagina **Configurazione e criteri** (selezionare **X** nell'angolo in alto a destra) per tornare alla home page del lab.  

## <a name="delete-a-mandatory-artifact"></a>Eliminare un artefatto obbligatorio
Per eliminare un artefatto obbligatorio da un lab, seguire questa procedura: 

1. Selezionare **Configurazione e criteri** in **IMPOSTAZIONI**. 
2. Selezionare **Artefatti obbligatori** in **RISORSE ESTERNE**. 
3. Selezionare **Modifica** nella sezione **Windows** o nella sezione **Linux**. Questo esempio usa l'opzione **Windows**. 
4. Selezionare il messaggio con il numero di artefatti obbligatori nella parte superiore. 

    ![Pagina Artefatti obbligatori - Selezionare il messaggio](media/devtest-lab-mandatory-artifacts/select-message-artifacts.png)
5. Nella pagina **Artefatti selezionati** selezionare **... (puntini di sospensione)** per l'artefatto da eliminare e selezionare **Rimuovi**. 
    
    ![Pagina Artefatti obbligatori - Rimuovere l'artefatto](media/devtest-lab-mandatory-artifacts/remove-artifact.png)
6. Selezionare **OK** per chiudere la pagina **Artefatti selezionati**. 
7. Selezionare **Salva** nella pagina **Artefatti obbligatori**.
8. Ripetere i passaggi per le immagini **Linux**, se necessario. 
9. Selezionare **Salva** per salvare tutte le modifiche al lab. 

## <a name="view-mandatory-artifacts-when-creating-a-vm"></a>Visualizzare gli artefatti obbligatori quando si crea una VM
A questo punto, un utente del lab può visualizzare l'elenco di artefatti obbligatori durante la creazione di una VM nel lab. Non è possibile modificare o eliminare gli artefatti obbligatori impostati nel lab dal proprietario del lab.

1. Nella home page del lab selezionare **Panoramica** dal menu.
2. Per aggiungere una VM al lab selezionare **+ Aggiungi**. 
3. Selezionare un'**immagine di base**. Questo esempio usa **Windows Server, versione 1709**.
4. Si noti che viene visualizzato un messaggio per gli **artefatti** con il numero di artefatti obbligatori selezionati. 
5. Selezionare **Elementi**. 
6. Verificare che siano visualizzati gli **artefatti obbligatori** specificati nella configurazione e nei criteri del lab. 

    ![Creare una VM - Artefatti obbligatori](media/devtest-lab-mandatory-artifacts/create-vm-artifacts.png)

## <a name="next-steps"></a>Passaggi successivi
* Informazioni su come [Aggiungere un archivio elementi Git a un lab](devtest-lab-add-artifact-repo.md).

