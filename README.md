# 𒀭𒈹 docker-inanna

> *an gal-ta ki gal-še₃ ĝeštug₂-ga-ni na-an-gub*
> From the great heaven she set her mind on the great below.
> — *Inana's descent to the nether world*, line 1

**Wipe Docker completely, in seven gates.** Every container, image, custom network, volume and byte of build cache is removed. In the Sumerian poem, Inanna, queen of heaven, has a piece of her regalia taken at each of the seven gates of the underworld. Here, Docker gives up one kind of thing at each gate.

## Why

Stale images, build cache and leftover volumes cause cache clashes, and `docker system prune -a --volumes` looks like the fix. It isn't enough. Since Docker 23 it only removes *anonymous* volumes, so named volumes survive, and so do the databases in them. docker-inanna removes everything, and makes an occasion of it.

> [!WARNING]
> **This can't be undone.** It acts on your current Docker context as soon as you run it, with no confirmation. Every volume goes too, named ones included, along with any databases in them.

## Install

```bash
git clone https://github.com/AndrewBewseyTNA/docker-inanna.git
ln -s "$PWD/docker-inanna/docker-inanna" ~/.local/bin/docker-inanna
```

To also make it a Docker CLI plugin, so `docker inanna` works:

```bash
mkdir -p ~/.docker/cli-plugins
ln -s "$PWD/docker-inanna/docker-inanna" ~/.docker/cli-plugins/docker-inanna
```

It needs the Docker CLI and bash 3.2 or later, so the version macOS ships works. The cuneiform needs a font that has it: Noto Sans Cuneiform, or Segoe UI Historic on Windows.

## Usage

```bash
docker-inanna                               # the descent
docker inanna                               # the same, as a Docker plugin
docker-inanna "docker compose up --build"   # descend, then ascend with this command
INANNA_PACE=0 docker-inanna                 # skip the ritual pauses
```

If you give no ascent command and the current directory has a compose file (`compose.yaml`, `docker-compose.yml`, …), it asks whether to bring it back up with `docker compose up -d`. It waits 15 seconds for an answer, then leaves it down.

| | |
|---|---|
| `-h`, `--help` | Show help |
| `-V`, `--version` | Show the version |
| `INANNA_PACE` | Seconds between ritual steps (default 1.3; `0` for none) |
| `NO_COLOR` | Turn colour off. Colour is also off when output isn't a terminal. |

When the ascent command fails, docker-inanna exits with that command's status.

As a plugin, it receives docker's global flags ahead of its own name. So `docker --context prod inanna` is refused rather than run against the wrong daemon. Use `DOCKER_CONTEXT=prod docker inanna` instead.

## The seven gates

Ereshkigal, queen of the underworld, orders Neti, her chief doorman, to bolt the seven gates and open them to Inanna one at a time. Something is taken from her at each gate. At each one she asks "What is this?" and gets the same reply: *"Be satisfied, Inanna: a divine power of the underworld has been fulfilled. Inanna, you must not open your mouth against the rites of the underworld."*

| Gate | Taken from Inanna | Taken from Docker | Because |
|---|---|---|---|
| 𒁹 I | the šugurra, the crown of the steppe, from her head | running containers (`docker kill`) | the crown is what currently reigns |
| 𒈫 II | the small lapis-lazuli beads, from her neck | every container (`docker rm -f`) | many small things on one string |
| 𒐈 III | the twin egg-shaped beads, from her breast | images (`docker rmi -f`) | eggs, which containers hatch from |
| 𒐉 IV | the pectoral called "Come, man, come!", from her breast | custom networks (`docker network prune`) | the call that draws others to her |
| 𒐊 V | the golden ring, from her hand | volumes, named ones too (`docker volume rm`) | gold doesn't tarnish, so it stands for what persists |
| 𒐋 VI | the lapis-lazuli measuring rod and measuring line, from her hand | build cache (`docker builder prune -a`) | the builder's tools |
| 𒐌 VII | the pala dress, the garment of ladyship, from her body | anything left (`docker system prune -a --volumes`) | the last covering |

The gate numbers are cuneiform numerals. Each gate reports what it took, and at the end Ereshkigal's tribute tells you how much disk space came back.

## The myth

Inanna sets her mind on the great below and goes down to the underworld, where her sister Ereshkigal rules. She passes through the seven gates, gives up her regalia one piece at a time, and arrives naked. She takes her sister's throne. The Anunna, the seven judges, give her the look of death, and she is turned into a corpse and hung on a hook.

Three days and three nights later, her minister Ninshubur pleads with the gods for help. Enlil refuses, and so does Nanna. Enki doesn't. From the dirt under his fingernails he makes two creatures, the kurgarra and the galatur. They sprinkle the life-giving plant and the life-giving water on the corpse, and Inanna rises. "Who has ever ascended unscathed from the underworld?" Demons called galla follow her up to claim a substitute, and her husband Dumuzi is the one who pays.

Your ascent is the command you give it, or `docker compose up`. It isn't free either: every layer has to be pulled again.

This follows the Sumerian poem. The later Akkadian *Descent of Ishtar* tells the story with different regalia, in a different order.

## Notes

- **WSL2:** a wipe frees space inside your distro, but its virtual disk (`ext4.vhdx`) doesn't give that space back to Windows. From PowerShell, run `wsl --shutdown`, then `wsl --manage <distro> --set-sparse true`. After that, freed space is reclaimed automatically.
- **Scope:** only the daemon behind your current Docker context is touched. Nothing in `~/.docker` changes: logins, contexts and buildx builder definitions all stay.
- **Spelling:** Oxford's ETCSL writes *Inana*, following the Sumerian ᵈinana. *Inanna* is the usual English spelling.

## Sources

- *Inana's descent to the nether world* (ETCSL 1.4.1), The Electronic Text Corpus of Sumerian Literature, University of Oxford: [translation](https://etcsl.orinst.ox.ac.uk/section1/tr141.htm) and [Sumerian text](https://etcsl.orinst.ox.ac.uk/section1/c141.htm). The script's wording follows this translation.
- Diane Wolkstein and Samuel Noah Kramer, *Inanna, Queen of Heaven and Earth* (1983).

> *kug ᵈereš-ki-gal-la-ke₄ za₃-mi₂-zu dug₃-ga-am₃*
> Holy Ereshkigal, sweet is your praise.
> — the poem's last line

## License

[MIT](LICENSE)
