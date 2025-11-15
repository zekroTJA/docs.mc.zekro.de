# ImageFrame

[ImageFrame](https://modrinth.com/plugin/imageframe) ist ein Plugin, welches es einem erlaubt eigene Bilder von einer URL in einer Map auf dem Server zu platzieren.

![](/assets/imageframe-example.png)

Folgendes Video-Tutorial zeigt sehr gut, wie man das Plugin benutzt:

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/N0PnFbWPD1A?si=CozY2H5EJR1kJu9h" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

## Commands

### `/imageframe create <name> <url> <width> <height> <mode>`

Erstellt eine Image-Map von der angegebenen URL mit der angegebenen Höhe (`<height>`) und Breite (`<width>`) **in Item-Frames**. Als Mode sollte man bei mehreren Frames am besten `combined` nutzen, dam an so alle Frames in einer Karte vereinen kann und nicht alle einzeln platzieren muss.

Das Bild unter der angegebenen URL wird am Ende unter dem angegebenen Namen (`<name>`) gespeichert und kann mit dem Command `/imageframe get` erneut geholt werden.

> ⚠️ **Achtung**: Man muss die benötigte anzahl an Karten im Inventar haben, bevor man den Command ausführt.

> 💡 **Tipp**: Wenn man ein Bild von seinem PC nutzen möchte, so ladet das Bild einfach in Discord hoch, öffnet das Bild im Browser und kopiert die URL des Bildes aus der URL-Zeile des Browsers.

### `/imageframe get <name> <mode>`

Gibt einem ein vorher hochgeladenes Bild als Map anhand des angegebenen Namens.

### `/imageframe list`

Listet alle hochgeladenen Bilder auf.

### `/imageframe delete <name>`

Löst ein hochgeladenes Bild anhand des angegebenen Namens.

> ⚠️ **Achtung**: Dies löscht auch das Bild als Datei auf dem Server, womit alle Frames, die dieses Bild haben kaputtgehen!
