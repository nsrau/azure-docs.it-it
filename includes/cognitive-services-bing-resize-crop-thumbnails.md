Alcune risposte di Bing includono gli URL delle immagini di anteprima servite da Bing. È possibile ridimensionare e ritagliare le immagini di anteprima. Per ridimensionare un'immagine, includere i parametri di query w (larghezza) e h (altezza) nell'URL dell'anteprima. Specificare la larghezza e l'altezza in pixel. Ad esempio:  
  
`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=200&h=200`  
  
Se si ridimensiona l'immagine, le proporzioni vengono mantenute. Per mantenere le proporzioni, potrebbe essere aggiunta una spaziatura interna bianca al bordo dell'immagine. Se ad esempio si ridimensiona un'immagine 480x359 a 200x200 senza ritagliarla, la larghezza completa contiene l'immagine, ma l'altezza contiene 25 pixel di spaziatura interna bianca nella parte superiore e inferiore dell'immagine. Sarà lo stesso se l'immagine è 359x480, ma saranno i bordi sinistro e destro a contenere la spaziatura interna bianca. Se si ritaglia l'immagine, la spaziatura interna bianca non viene aggiunta.  
  
La figura seguente illustra le dimensioni originali di un'immagine di anteprima (480x300).  
  
![Immagine orizzontale originale](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape.PNG)  
  
La figura seguente illustra l'immagine ridimensionata a 200x200. Le proporzioni vengono mantenute e i bordi superiore e inferiore vengono riempiti di bianco. Il bordo nero è stato incluso per indicare la spaziatura interna.  
  
![Immagine orizzontale ridimensionata](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape-resized.PNG)  
  
Se si specificano dimensioni maggiori della larghezza e dell'altezza originali dell'immagine, l'immagine viene riempita di bianco in corrispondenza dei bordi sinistro e superiore.  
  
Per ritagliare un'immagine, includere il parametro di query c (ritaglio). Di seguito sono indicati i valori possibili che si possono specificare.  
  
- 4: proporzioni casuali  
- 7: proporzioni intelligenti  
  
Se si richiede il ritaglio con proporzioni intelligenti (c=7), l'immagine viene ritagliata dal centro dell'area di interesse dell'immagine verso l'esterno, mantenendo tuttavia le proporzioni dell'immagine. L'area di interesse è la parte dell'immagine che secondo Bing contiene gli elementi più importanti. Il seguente è un esempio di area di interesse.  
  
![Area di interesse](./media/cognitive-services-bing-resize-crop/bing-resize-crop-regionofinterest.PNG)

Se si ridimensiona un'immagine e si richiede il ritaglio con proporzioni intelligenti, l'immagine viene ridotta alle dimensioni richieste mantenendo tuttavia le proporzioni. L'immagine viene quindi ritagliata in base alle dimensioni modificate. Se ad esempio la larghezza ridimensionata è inferiore o uguale all'altezza, l'immagine viene ritagliata a sinistra e a destra del centro dell'area di interesse. In caso contrario, l'immagine viene ritagliata sopra e sotto il centro dell'area di interesse.  
  
La seguente è l'immagine ridotta a 200x200 usando il ritaglio con proporzioni intelligenti.  
  
![Immagine orizzontale ritagliata a 200x200](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape200x200c7.PNG)
  
La seguente è l'immagine ridotta a 200x100 usando il ritaglio con proporzioni intelligenti.  
   
![Immagine orizzontale ritagliata a 200x100](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape200x100c7.PNG)
  
La seguente è l'immagine ridotta a 100x200 usando il ritaglio con proporzioni intelligenti.  
  
![Immagine orizzontale ritagliata a 100x200](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape100x200c7.PNG)
  
Se Bing non riesce a determinare l'area di interesse dell'immagine, usa il ritaglio con proporzioni casuali.  
  
Se si richiede il ritaglio con proporzioni casuali (c=4), Bing usa le regole seguenti per ritagliare l'immagine.  
  
- Se (larghezza immagine originale/altezza immagine originale) < (larghezza immagine richiesta/altezza immagine richiesta), l'immagine viene misurata dall'angolo in alto a sinistra e ritagliata in basso.  
- Se (larghezza immagine originale/altezza immagine originale) > (larghezza immagine richiesta/altezza immagine richiesta), l'immagine viene misurata dal centro e ritagliata a sinistra e a destra.  
  
La seguente è un'immagine verticale 225x300.  
  
![Immagine di girasole originale](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower.PNG)
  
La seguente è l'immagine ridotta a 200x200 usando il ritaglio con proporzioni casuali. L'immagine viene misurata dall'angolo in alto a sinistra e di conseguenza ne viene ritagliata la parte inferiore.  
  
![Immagine di girasole ritagliata a 200x200](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower200x200c4.PNG)
  
La seguente è l'immagine ridotta a 200x100 usando il ritaglio con proporzioni casuali. L'immagine viene misurata dall'angolo in alto a sinistra e di conseguenza ne viene ritagliata la parte inferiore.  
  
![Immagine di girasole ritagliata a 200x100](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower200x100c4.PNG)
  
La seguente è l'immagine ridotta a 100x200 usando il ritaglio con proporzioni casuali. L'immagine viene misurata dal centro e di conseguenza ne vengono ritagliate le parti sinistra e destra.  
  
![Immagine di girasole ritagliata a 100x200](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower100x200c4.PNG)
