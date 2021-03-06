---
title: Cross-Platform Support
page_title: Cross-Platform Support
description: Cross-Platform Support
slug: radpdfprocessing-cross-platform
tags: cross,platform
published: True
position: 2
---

# Cross-Platform Support

**Telerik Document Processing** comes with **.NET Core** & **.NET Standard** support. There is a set of binaries built against the .NET Core & .NET Standard which you can reference in an application.

## Assembly References

In order to use the model of the **RadPdfProcessing** library in your cross-platform project, you need to add references to the following **.Net Standard** assemblies:

* **Telerik.Documents.Core.dll**
* **Telerik.Documents.Fixed.dll**
* **Telerik.Zip.dll**

> Note that for .NET Framework & .NET Core with Windows Compatibility Pack projects, the references contain "Windows" in their names (e.g. **Telerik.Windows.Documents.Core.dll**)

## What's New

In the .NET Framework version of PdfProcessing, scenarios like reading fonts are something that comes out of the box. However, the .NET Standard doesn't specify APIs to provide this functionality built in the library, so there are some differences in both versions of PdfProcessing.

### New APIs

The [limitations in .Net Standard](#limitations-in-net-standard) require some additional settings to be done, therefore, the **RadPdfProcessing** library exposes a FixedExtensibilityManager class used for providing extensibility mechanisms.

#### FixedExtensibilityManager class

The new **FixedExtensibilityManager** class is exposing one property:

* **FontsProvider**: Gets or sets a *FontsProviderBase* instance used to provide missing fonts.

    >note .NET Standard specification does not define APIs for getting specific fonts. PdfProcessing needs to have access to the font data so that it can read it and add it to the PDF file. That is why, to allow the library to create and use fonts, you will need to provide an implementation of the FontsProviderBase abstract class and set this implementation to the FontsProvider property of FixedExtensibilityManager.
    
    >important If the FontsProvider property is not set, a default font will be used when exporting the document.

    #### **[C#] Example 1: Windows Example: Creating custom implementation inheriting the FontsProviderBase abstract class**
    {{region cs-radpdfprocessing-cross-platform_0}}

        public class FontsProvider : FontsProviderBase
        {
            public override byte[] GetFontData(FontProperties fontProperties)
            {
                string fontFileName = fontProperties.FontFamilyName + ".ttf";
                string fontFolder = Environment.GetFolderPath(Environment.SpecialFolder.Fonts);
                string targetPath = Path.Combine(fontFolder, fontFileName);

                DirectoryInfo directory = new DirectoryInfo(fontFolder);
                FileInfo[] fontFiles = directory.GetFiles("*.ttf");
                if (fontFiles.Any(s => s.Name.ToLower() == fontFileName.ToLower()))
                {
                    using (FileStream fileStream = File.OpenRead(targetPath))
                    {
                        using (MemoryStream memoryStream = new MemoryStream())
                        {
                            fileStream.CopyTo(memoryStream);
                            return memoryStream.ToArray();
                        }
                    }
                }

                return null;
            }
        }
    {{endregion}}

    #### **[C#] Example 2: Set the custom implementation inheriting the FontsProviderBase abstract class**
    {{region cs-radpdfprocessing-cross-platform_1}}

        FontsProviderBase fontsProvider = new FontsProvider();
        FixedExtensibilityManager.FontsProvider = fontsProvider;
    {{endregion}}


## Limitations in .Net Standard

### Additional settings required

Some functionalities require additional settings to be done:

* In order to export specific fonts, the FontsProvider property inside the FixedExtensibilityManager have to be set.

### Currently not supported

 - Referencing .Net Standard binaries you can add SignatureField but can not sign or import signed documents.
 - **PNG** images are **not** supported. Due to framework limitations, **only JPEG and JPEG2000 are supported**.
 
## See Also

 * [RadFixedDocument]({%slug radpdfprocessing-model-radfixeddocument%})
 * [RadFixedPage]({%slug radpdfprocessing-model-radfixedpage%})
 * [FixedContentEditor]({%slug radpdfprocessing-editing-fixedcontenteditor%})
 * [TextFragment]({%slug radpdfprocessing-model-textfragment%})
 * [PDF Format]({%slug radpdfprocessing-formats-and-conversion-pdf-pdf%})
