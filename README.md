# Phabricator Plugin for Vim

This [Vim](https://www.vim.org/) plugin provides features for interacting with
[Phabricator](https://phacility.com/phabricator/)-based repositories. It
builds upon the foundational git functionality provided by [Fugitive][].

* Syntax highlighting for Arcanist templates and configuration files
* `:Gbrowse` support for browsing Phabricator URLs from the current buffer
* Completion support for users and projects in Arcanist diff templates using
  [`<C-X><C-O>`][compl-omni] (requires [curl][] and a [Conduit API][conduit]
  token)

[fugitive]: https://github.com/tpope/vim-fugitive
[compl-omni]: http://vimdoc.sourceforge.net/htmldoc/insert.html#compl-omni
[curl]: https://curl.haxx.se/

## Installation

### Using [lazy-vim](https://github.com/folke/lazy.nvim)

```lua
	{
		"lanej/vim-phabricator",
		dependencies = {
			"tpope/vim-fugitive",
		},
		enabled = function()
			return vim.fn.filereadable(vim.fn.expand("~/.arcrc")) == 1
		end,
		config = function()
			-- Read the arcrc file, parse the json into a lua table
			local arcrc = vim.fn.json_decode(vim.fn.readfile(vim.fn.expand("~/.arcrc")))
			-- The base url is the config.default value
			vim.g.phabricator_hosts = { arcrc.config.default }
			-- The Token is is in the hosts table, with a key of the base url + "/api/"
			vim.g.phabricator_api_token = arcrc.hosts[arcrc.config.default .. "/api/"].token
		end,
	},

```

### Using [vim-plug][plug]

1. Add `Plug 'lanej/vim-phabricator'` to `~/.vimrc`
2. `vim +PluginInstall +qall`

[plug]: https://github.com/junegunn/vim-plug

### Using Vim Packages

```sh
mkdir -p ~/.vim/pack/lanej/start
cd ~/.vim/pack/lanej/start
git clone https://github.com/lanej/vim-phabricator.git phabricator
vim -u NONE -c "helptags phabricator/doc" -c q
```

## Configuration

### `g:phabricator_hosts`

This plugin automatically recognizes Phabricator repositories served from
hosts named `phabricator` (eg. `phabricator.example.com`).

This variable lists additional hosts that should also be considered
Phabricator hosts.

### `g:phabricator_api_token`

[Phabricator's Conduit API][conduit] is used to generate candidates for user
and project completion. These API calls require an access token which can be
generated in Phabricator's per-user Settings interface. For example:

    https://phabricator.example.com/settings/user/USERNAME/page/apitokens/

The plugin will first attempt to read the per-host API token from the user's
`~/.arcrc` configuration file. For example:

```json
{
  "hosts": {
    "https://secure.phabricator.com/api/": {
      "token": "api-secrettokencharacters"
    }
  }
}
```

If an API token can not be read from the `~/.arcrc` file for the current
Phabricator host, the value stored in `g:phabricator_api_token` will be used.

[conduit]: https://secure.phabricator.com/book/phabricator/article/conduit/

## License

This code is released under the terms of the MIT license.
See [`LICENSE`](LICENSE) for details.

## Similar Projects

* [arcanist.vim](https://github.com/solarnz/arcanist.vim) provides syntax
  highlighting for Arcanist templates and configuration files.
* [phabrowser.vim](https://github.com/peplin/vim-phabrowse) provides a Fugitive
  browse handler for Phabricator.
* [rhubarb.vim](https://github.com/tpope/vim-rhubarb) provides similar features
  for GitHub-based repositories.
* [vscode-phabricator](https://github.com/christianvuerings/vscode-phabricator)
  provides similar Phabricator features for Visual Studio Code.
