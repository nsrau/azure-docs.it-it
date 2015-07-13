Ogni BLOB nell'archiviazione di Azure deve risiedere in un contenitore. Il contenitore fa parte del nome del BLOB. Ad esempio, `mycontainer` è il nome del contenitore nei seguenti URI del BLOB di esempio:

	https://storagesample.blob.core.windows.net/mycontainer/blob1.txt
	https://storagesample.blob.core.windows.net/mycontainer/photos/myphoto.jpg
 
> [AZURE.IMPORTANT]Il nome di un contenitore deve sempre essere minuscolo. Se si include una lettera maiuscola nel nome di un contenitore o si violano in altro modo le regole di denominazione del contenitore, è possibile che venga visualizzato un errore 400 (richiesta non valida). Per le regole sulla denominazione dei contenitori, vedere [Assegnazione di nome e riferimento a contenitori, BLOB e metadati](https://msdn.microsoft.com/library/azure/dd135715.aspx).

<!---HONumber=62-->