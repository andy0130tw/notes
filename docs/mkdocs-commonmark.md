# Mkdocs <small>with CommonMark</small>

This is my attempt to bring CommonMark to mkdocs, meant to be consistent and reliable. Currently it uses [mistletoe](https://github.com/miyuchina/mistletoe) as the core parser, which fully passes CommonMark v0.28. The development continues to also port various extensions, and to document everything involved.

## Motivation

mkdocs is a good project documentation generator in the first sight. It is simple, straightforward, and with beautiful themes availble. But pairing with [Python-Markdown](https://github.com/Python-Markdown/markdown) over those years, the syntax is always mysterious to me. With a [loosely-defined](https://github.com/mkdocs/mkdocs/issues/361) syntax, one needs to read the output very carefully just to make sure what the parser renders agrees with what the author types. This makes writing documentations painful and unproductive.

> I don't think I see why documentation specifically needs the syntax to be more less precisely defined than other prose, but that is another discussion :)

I am opposed to this. Markdown, as [its author originally states](https://daringfireball.net/projects/markdown/), is designed to be as readable as possible. In other words, the final result should be obviously predictable by just reading the source. As Python-Markdown only support a conservative set of Markdown features, many of our familiar Markdown features are implemented as "official extensions", like tables, fenced code, code hightlighting, this really complicates the developers if the result is inconsistent between the highlighting of the text editor, the preview program, or even different version of the same package. Motivated by this, I decide to replace the core Markdown engine with one that follows a spec.

## The structure

Originally, every Markdown document walks through the following stages to produce the final HTML text:

  * **preprocessors**: receives lines, produces lines
  * **blockprocessors**: group lines and construct HTML tree from these chunks
  * **treeprocessors**: modify the tree, in which InlineProcessor seeks inline patterns by regex
  * **postprocessors**: final modifications to the HTML tree
  * other minor tasks like serializing HTML to a string

Mistletoe takes control over only the two stages: preprocessors and blockprocessors. A custom renderer `ETreeRenderer` is introduced in this project, allowing mistletoe to produce similar output to continue the process as normal. With an HTML serializer, this can be end-to-end tested against the CommonMark test suite. On the contrary, extensions that tweak the block processors is surely no longer to work. As the idea of Python-Markdown and mistletoe is very different, I can't come up with a way to mock this. Even re-implementing in mistletoe seems acceptable.

## WIP
