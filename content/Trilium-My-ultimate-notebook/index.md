+++
title = "Trilium, My ultimate notebook"
date = 2022-10-10T01:22:08+08:00
updated = 2022-10-10T01:22:08+08:00
in_search_index = true

[taxonomies]
tags = [ "trilium", "self-host", "knowledge base", "notebook"]
categories = [ "Notes",]
archives = [ "archive",]
+++

# TL;DR

I transferred my notes to self-hosted notebook software called [trilium](https://github.com/zadam/trilium), and tried tweaking it to fit me.

I'll list the [pros & cons](#pros-cons) of it in my opinion below.

<!-- more -->

# Background

I used to use simplenote as my notebook, it runs blazing fast, has cloud synchronize and tag system. I used to make it as my diary and notebook for random thoughts. ![simplenote](simplenote.png) ![tag](simplenote_tags.png).

As I grew older and create more and more side projects, I find it's not enough for my daily use. I want a notebook with such features:

- must have:
  - tag system
  - easy to classify and reorder
  - basic markdown support
  - can run as standalone application
  - can import notes from markdown files
- best to have:
  - document template(for my todo list and diary)
  - API support(because I'm a programmer)
  - self-host(I have a private server and public internet access to it)
    - cloud synchronize support(can be native support or through Onedrive/Google Drive)
  - what you see is what you get
    - custom CSS
  - advanced markdown support
    - math formula(LaTeX, mathjax)
    - mermaid diagram
  - handwriting support(I have a Surface)

I tried several notebook software/SaaS like notion/obsidian/logseq/OneNote and none of them satisfy my basic requirements, so I began to try software on <https://github.com/awesome-selfhosted/awesome-selfhosted#note-taking--editors>. Then I found trilium.

# Transfer to trilium

## Make a TODO list is my first TODO

You can get a straight view of how it looks like in its [screenshot tour](https://github.com/zadam/trilium/wiki/Screenshot-tour), or looking through [features list](https://github.com/zadam/trilium#features) to find something interesting, or check [author's opinion towards personal knowledge base](https://github.com/zadam/trilium/wiki/Patterns-of-personal-knowledge-base) to know what will trilium be.

For me, a programmer who loves changing random stuff and seeing what happens, just downloading and trying is my best choice. 

Opening up trilium without server, it will have the demo notes under root folder. They are great examples for me to know what can trilium do and how it is being done.

For me, the most interesting example is weight tracker. I used to have a self-hosted redmine help me manage my side projects, but full real project management software is too heavy to me to use daily. I just develop these packages by myself, all I need is a todo list and a wiki page for every project, and I want these todo lists to also show in a standalone page so I can know I have something to do.

Weight tracker's data is gathered through the notes which has the label "weight". Weight tracker's display is done by using JavaScript library chartjs.

Because I can use JavaScript library in notes, I can literally display anything on the page. Trilium also provide API to fetch note content/title/etc. Which means trilium is a notebook with flexibility as website.

I create a render note and write a script and, Voilà!

![todo](./todo.png)

Every title is a hyper link navigated to the project's todo note, even the page cannot edit and feed the change back to real todo note, it's good enough for me.

## Label system

Trilium has three special note types for journal: day note, month note, year note. Thanks to my well formatted diary notes title, I can easily write a script to reorganize these notes into the same order as trilium.

But after importing, I still cannot see these notes in trilium's built-in calendar. So I checked the difference between imported diary notes and diary notes created by trilium, and found out that diary notes created by trilium have a label whose value is date.

By default, the exported notes come with a file named `!!!meta.json` containing metadata. I tried to write a script to generate these metadata by hand, and failed. Every note have an ID, and I still don't know what will happen if I random some strings and put them into trilium.

Thank goodness, the flexibility of trilium exceeds my expectations. Finally, I use the bulk action run some script written in 5 minutes on these notes and set the labels.

# end

Now I use trilium on all my digital devices: laptop, desktop, mobile phone, tablet. I think trilium will be the notebook software for my life.

# pros & cons

pros:

1. open source software
1. markdown support
   1. math formula
   2. mermaid diagram
2. handwriting support
3. standalone application(store data locally)
4. can be hosted locally and provide synchronize service for instance runs on other devices
   1. hosted service also provide web access
5. import markdown/html/text
6. appendix support
7. API support with great flexibility
8. what you see is what you get with custom CSS support
9. can export markdown
10. plugin support

cons:

1. markdown is not the internal file format
   1. cannot integrate mermaid diagram into notes  
   2. cannot render markdown on the run
2. performance seems not good for too many notes
3. not simple
4. trilium is on beta(means may introduce breaking change in the future)

# Appendix

## script which reorganize notes

``` python
# %%
import json

# %%
with open('notes.json', 'r') as f:
    notes = json.loads(f.read())

# %%
tagged_notes = {}
for note in notes['activeNotes']:
    if 'tags' in note.keys():
        for tag in note['tags']:
            tagged_notes.setdefault(tag, []).append(note)
    else:
        tagged_notes.setdefault('untagged', []).append(note)

# %%
years = ['2016', '2017', '2018', '2019', '2020', '2021', '2022']
months = ['01-January', '02-February', '03-March', '04-April', '05-May', '06-June', '07-July', '08-August', '09-September', '10-October', '11-November', '12-December']
from pathlib import Path
for year in years:
    for month in months:
        Path('./{}/{}'.format(year, month)).mkdir(parents=True, exist_ok=True)
for tag in tagged_notes.keys():
    if tag != '日总结' and tag != '月总结' and tag != '周总结':
        Path('./{}'.format(tag)).mkdir(parents=True, exist_ok=True)
Path('./ToClassified').mkdir(parents=True, exist_ok=True)

# %%
for note in tagged_notes['日总结']:
    title = note['content'].split('\r\n')[0].lstrip('#').lstrip()
    content = '\n\n'.join(note['content'].split('\r\n')[2:])
    try:
        if title[0:4] not in years or int(title[5:7]) > 12 or int(title[5:7]) < 1:
            raise ValueError('')
        with open('./{}/{}/{}.md'.format(title[0:4], months[int(title[5:7]) - 1], title), 'w') as f:
            f.write(content)
    except:
        print(title)
        with open('./ToClassified/{}.md'.format(title), 'w') as f:
            f.write(content) 

# %%
import re
for note in tagged_notes['月总结']:
    title = note['content'].split('\r\n')[0].lstrip('#').lstrip()
    content = '\n\n'.join(note['content'].split('\r\n')[2:])
    try:
        month = int(re.findall(r"\d+", title)[1])
        if month > 12 or month < 1:
            raise ValueError('')
        with open('./{}/{}.md'.format(title[0:4], months[month - 1]), 'w') as f:
            f.write(content)
    except:
        print(title)
        with open('./ToClassified/{}.md'.format(title), 'w') as f:
            f.write(content) 

# %%
for note in tagged_notes['周总结']:
    title = note['content'].split('\r\n')[0].lstrip('#').lstrip()
    content = '\n\n'.join(note['content'].split('\r\n')[2:])
    year = note['creationDate'][0:4]
    month = int(note['creationDate'][5:7])
    try:
        if month > 12 or month < 1:
            raise ValueError('')
        with open('./{}/{}/{}.md'.format(year, months[month - 1], title), 'w') as f:
            f.write(content)
    except:
        print(title)
        with open('./ToClassified/{}.md'.format(title), 'w') as f:
            f.write(content) 

# %%
for tag, notes in tagged_notes.items():
    if tag == '日总结' or tag == '周总结' or tag == '月总结':
        continue
    for note in notes:
        try:
            title = note['content'].split('\r\n')[0].lstrip('#').lstrip().replace('/', ' ')
            content = '\n\n'.join(note['content'].split('\r\n')[2:])
            with open('./{}/{}.md'.format(tag, title), 'w') as f:
                f.write(content)
        except:
            title = note['content'].split('\r\n')[0].lstrip('#').lstrip()[:20].replace('/', ' ')
            content = '\n\n'.join(note['content'].split('\r\n'))
            with open('./{}/{}.md'.format(tag, title), 'w') as f:
                f.write(content)
```

## scripts which bulk add label

``` javascript
if(/^\\d{4}$/.test(note.title)){note.addAttribute('label', 'yearNote', note.title)}
if (note.title.match(/\\d{4}-\\d{2}-\\d{2}/).length == 1) { note.addAttribute('label', 'dateNote', note.title.match(/\\d{4}-\\d{2}-\\d{2}/)[0]) }
if (note.title.match(/^(\\d{2})-([a-zA-Z]+)$/).length == 3) { note.title = note.title.match(/^(\\d{2})-([a-zA-Z]+)$/)[1] + ' - ' + note.title.match(/^(\\d{2})-([a-zA-Z]+)$/)[2] }
if (note.title.match(/^(\\d{2}) - ([a-zA-Z]+)$/).length == 3) { note.addAttribute('label', 'monthNote', `2021-${note.title.match(/^(\\d{2}) - ([a-zA-Z]+)$/)[1]}`) }
```

## script which gathers todo and display them

``` javascript
const getTodo = async () => {
    const todos = await api.runOnServer(() => {
        const notes = api.getNotesWithLabel('todo');
        return notes.map(x => ({
            title: x.getParentNotes()[0].title,
            open: !!x.getAttribute('label', 'alwaysShow'),
            nav: x.getAllNotePaths()[0],
            content: x.getContent()
        }));
    });
    return todos;
};

const todos = await getTodo();
const container = document.getElementById('todo')
container.innerHTML = ''
for(const todo of todos) {
    const newTodo = document.createElement('details')
    newTodo.innerHTML = todo.content
    const newTodoTitle = document.createElement('summary')
    const newTodoNav = document.createElement('a')
    newTodoNav.href = '#'
    newTodoNav.href += todo.nav.join('/')
    const header = document.createElement('h1')
    header.innerHTML = todo.title
    header.style.display = 'inline'
    newTodoNav.appendChild(header)
    newTodoTitle.appendChild(newTodoNav)
    newTodo.appendChild(newTodoTitle)
    newTodo.open = todo.open
    container.appendChild(newTodo)
}
```
