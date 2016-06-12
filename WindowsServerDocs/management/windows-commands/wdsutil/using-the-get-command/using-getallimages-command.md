---
title: Using the get-AllImages Command
ms.custom: na
ms.prod: windows-server-2012
ms.reviewer: na
ms.suite: na
ms.tgt_pltfrm: na
ms.topic: article
ms.assetid: 19de3720-4315-415a-8dc6-486caa0b2100
---
# Using the get-AllImages Command
Retrieves information about all images on a server.

## Syntax

```
wdsutil /Get-AllImages [/Server:<Server name>] /Show:{Boot | Install | LegacyRis | All} [/detailed]
```

## Parameters

|Parameter|Description|
|-------------|---------------|
|\[\/Server:<Server name>\]|Specifies the name of the server. This can be either the NetBIOS name or the fully qualified domain name \(FQDN\). if no server name is specified, the local server will be used.|
|\/Show: {Boot &#124; Install &#124; LegacyRis &#124; All}|-   **Boot** returns only boot images.<br />-   **Install** returns install images as well as information about the image groups that contain them.<br />-   **LegacyRis** returns only remote Installation Services \(RIS\) images.<br />-   **All** returns boot image information, install image information \(including information about the image groups\), and RIS image information.|
|\[\/detailed\]|Indicates that all image metadata from each image should be returned. if this option is not used, the default behavior is to return only the image name, description, and file name.|

## <a name="BKMK_examples"></a>Examples
To view information about the images, type one of the following:

```
wdsutil /Get-AllImages /Show:Install
wdsutil /verbose /Get-AllImages /Server:MyWDSServer /Show:All /detailed
```

#### additional references
[Command-Line Syntax Key](../../commandline-syntax-key.md)

[Using the add-Image Command](../using-add-command/using-addimage-command.md)

[Using the copy-Image Command](../using-copy-command/using-copyimage-command.md)

[Using the Export-Image Command](../using-exportimage-command.md)

[Using the remove-Image Command](../using-remove-command/using-removeimage-command.md)

[Using the replace-Image Command](../using-replaceimage-command.md)

[Subcommand: set-Image](../the-set-command/subcommand-setimage.md)

