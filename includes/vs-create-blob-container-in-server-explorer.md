È possibile creare le code di archiviazione Azure tramite **Esplora Server** di Visual Studio.

![BLOB in Esplora server][Image1]

1. Scegliere **Esplora server** dal menu **Visualizza**.
2. In Esplora server espandere il nodo **Azure** per la sottoscrizione, espandere il nodo **Archiviazione** e il nodo per l'account di archiviazione specificato nel servizio Archiviazione di Azure connesso.
3. Selezionare il nodo**Code**e scegliere**Creare coda**dal menu di scelta rapida.
4. Immettere un nome per il contenitore e scegliere **OK**.   

Per impostazione predefinita, il nuovo contenitore è privato ed è necessario specificare la chiave di accesso alle risorse di archiviazione per scaricare BLOB da questo contenitore. Per rendere pubblici i file nel contenitore, selezionare il contenitore in **Esplora server**e premere `F4` per visualizzare la finestra **Proprietà**. Impostare **Accesso in lettura pubblico** su **BLOB**. I BLOB in un contenitore pubblico sono visibili a tutti gli utenti di Internet, tuttavia è possibile modificarli o eliminarli solo se si dispone della chiave di accesso appropriata.


[Image1]: ./media/vs-create-blob-container-in-server-explorer/vs-storage-create-blob-containers-in-Server-Explorer.png

<!---HONumber=Oct15_HO3-->