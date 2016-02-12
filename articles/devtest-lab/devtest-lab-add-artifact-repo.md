<properties
	pageTitle="Aggiungere un repository di elementi Git al Lab di sviluppo/test | Microsoft Azure"
	description="Aggiungere un repository di elementi Git di GitHub o Visual Studio Team Services per gli elementi personalizzati del lab"
	services="devtest-lab,virtual-machines,visual-studio-online"
	documentationCenter="na"
	authors="tomarcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="devtest-lab"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/03/2016"
	ms.author="tarcher"/>

# Aggiungere un archivio elementi Git al Lab di sviluppo e test

## Panoramica

Per impostazione predefinita, un lab di sviluppo e test include gli elementi dell’archivio elementi ufficiale di Lab di sviluppo e test. È possibile aggiungere un archivio elementi di Git al lab per includere gli elementi creati dal team. L’archivio può essere ospitato in [GitHub](https://github.com) o [Visual Studio Team Services (VSTS)](https://visualstudio.com).

- Per informazioni su come creare un archivio GitHub, vedere [GitHub Bootcamp](https://help.github.com/categories/bootcamp/).
- Per informazioni su come creare un progetto Team Services con un archivio Git, vedere [Connessione a Visual Studio Team Services](https://www.visualstudio.com/get-started/setup/connect-to-visual-studio-online).

Nella schermata seguente viene illustrato un esempio di come potrebbe apparire un archivio contenente gli elementi in GitHub: ![Esempio di archivio elementi GitHub](./media/devtest-lab-add-artifact-repo/devtestlab-github-artifact-repo-home.png)

## Aggiungere un archivio elementi di GitHub al lab

Per aggiungere un archivio elementi GitHub al lab, ottenere innanzitutto l’URL clone di HTTPS e il token di accesso personale dall'archivio elementi, quindi immettere tali informazioni nel lab

### Ottenere l'URL clone dell'archivio GitHub e il token di accesso personale

1. Nella home page dell'archivio GitHub che contiene gli elementi del team, salvare l’**url clone di HTTPS** per un utilizzo successivo.

1. Toccare l'immagine del profilo nell'angolo superiore destro e selezionare **Impostazioni**.

1. Nel menu **Impostazioni personali** a sinistra, toccare **Token di accesso personali**.

1. Toccare **Genera nuovo token**.

1. Nella pagina **Nuovo token di accesso personale** immettere una **Descrizione del token**, accettare le voci predefinite in **Seleziona ambiti**, e quindi scegliere **Genera token**.

1. Salvare il token generato poiché sarà necessario successivamente.

1. A questo punto, è possibile chiudere GitHub.

###Connettere il lab all'archivio GitHub

1. Accedere al [portale di anteprima di Azure](https://portal.azure.com).

1. Toccare **Sfoglia** e poi **Lab di sviluppo e test** dall'elenco.

1. Dall'elenco dei lab, toccare il lab desiderato.

1. Nel pannello del lab, toccare **Impostazioni**.

1. Nel pannello **Impostazioni** del lab, toccare **Archivio elementi**.

1. Nel pannello **Archivio elementi**:

    1. Immettere un **Nome** per l'archivio.
    1. Immettere l’**Url clone di Git** salvato.
    2. Immettere il **Percorso della cartella** nell’archivio elementi che contiene gli elementi.
    3. Immettere il **Token di accesso personale** salvato nell'archivio elementi.
    4. Toccare **Salva**.

Gli elementi nell’archivio sono ora elencati nel pannello **Aggiungi elementi**.

## Aggiunta di un archivio elementi Git di Visual Studio al lab

Per aggiungere un archivio elementi Git di Visual Studio al lab, ottenere innanzitutto l’URL clone di HTTPS e il token di accesso personale dall'archivio elementi, quindi immettere tali informazioni nel lab.

### Nella pagina web di Visual Studio del progetto dell’elemento

1. Aprire la home page della raccolta del team (ad esempio, `https://contoso-web-team.visualstudio.com`), quindi toccare il progetto dell’elemento.

2. Nella home page del progetto, toccare **Codice**.

1. Per visualizzare l'URL del clone, nella pagina **Codice** del progetto, toccare **Clone**.

1. Salvare l'URL poichè sarà necessario più avanti in questa esercitazione.

1. Per creare un Token di accesso personale, toccare **Il mio profilo** nel menu a discesa dell’account utente.

1. Nella pagina delle informazioni del profilo toccare **Sicurezza**.

1. Nulla scheda **Sicurezza**, toccare **Aggiungi**.

1. Nella pagina **Crea un token di accesso personale**:

    1. Immettere una **Descrizione** per il token.
    2. Selezionare **180 giorni** dall’elenco **Scade in**.
    3. Scegliere **Tutti gli account accessibili** dall’elenco **Account**.
    4. Scegliere l’opzione **Tutti gli ambiti**.
    5. Scegliere **Crea token**.

1. Al termine, verrà visualizzato il nuovo token nell’elenco **Token di accesso personale**. Toccare **Copia Token** e quindi salvare il valore del token in quanto verrà utilizzato al più presto.

### Nel lab di sviluppo e test

1. Nel proprio pannello del lab, toccare **Impostazioni**.

    ![Scegliere impostazioni.](./media/devtest-lab-add-artifact-repo/devtestlab-add-artifacts-repo-open-dtl-settings.png)

1. Nel pannello **Impostazioni**, toccare **Archivio elementi**.

1. Nel pannello **Archivio elementi**

    1. Immettere un **Nome** visualizzato per l'archivio.
    1. Immettere l’**Url clone di Git** salvato.
    2. Immettere il **Percorso della cartella** nell’archivio elementi che contiene gli elementi.
    3. Immettere il **Token di accesso personale** salvato nell'archivio elementi.
    4. Toccare **Salva**.

<!---HONumber=AcomDC_0204_2016-->