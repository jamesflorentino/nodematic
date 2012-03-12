Http = require 'http'
Url = require 'url'
Path = require 'path'
Fs = require 'fs'
Port = 3030

Jade = require 'jade'
Styl = require 'stylus'
Nib = require 'nib'
Coffee = require 'coffee-script'

Settings = {}

compile = (uri, filedata, cb) ->
	filename = uri.substr uri.lastIndexOf('/') + 1, uri.length
	fileext = filename.substr filename.lastIndexOf('.') + 1, filename.length
	data = filedata.toString 'ascii'
	if fileext.match /styl/
		Styl(data)
			.use(Nib())
			.render (err, css) ->
				cb css
	else if fileext.match /jade/
		cb Jade.compile(data, filename: 'source/layout.jade', locals: Settings).call()
	else if fileext.match /coffee/
		cb Coffee.compile(data)
	else
		cb()


task "server", "Run a server", ->
	Settings = Fs.readFileSync 'settings.json', 'ascii'
	Settings = JSON.parse Settings
	httpServer = Http.createServer (request, response) ->

		path = request.url
		path = '/index.html' if path is '/'
		uri = "source#{path}"
		uriDirectory = uri.substr 0, uri.lastIndexOf '/'
		Path.exists uriDirectory, (exists) ->

			if !exists
				response.writeHead 404, 'content-type': 'text/html'
				response.end '404 not found'
				return

			Path.exists uri, (exists) ->
				if !exists
					if Path.existsSync("#{uri}.styl")
						uri += '.styl'
					else if Path.existsSync("#{uri}.jade")
						uri += '.jade'
					else if Path.existsSync("#{uri}.coffee")
						uri += '.coffee'
					else
						response.writeHead 404, 'content-type': 'text/html'
						response.end '404 not found'
						return

				Fs.readFile uri, (err, data) ->
					if err
						response.writeHead 500, 'content-type':'text/html'
						response.end "#{err}\n"
						return

					compile uri, data, (chunk, type) ->
						chunk = data if !chunk
						response.writeHead 200
						response.write chunk
						response.end()

	httpServer.listen Port
	console.log "Site is running on localhost:#{Port}"















# CREATE THE PROJECT DIRECTORIES
task 'create', 'Create your project in the current directory', ->
	Stub =
		index:
			"""
			extends layout
			prepend head
				title Awesome title
			prepend body
				h1 Welcome to NodeMatic!
				p - a static site generator built on top of NodeJS
			"""
		layout:
			"""
			html
			block head
				link(href="stylesheets/site.css", rel="stylesheet")
				script(src="javascripts/app.js")
			block body
			"""
		app:
			"""
			# entry point
			"""
		styl:
			"""
			body
				background #eee
				color #333
			"""
		settings:
			"""
			{
				"title": "My awesomwe website"
			}
			"""
	Fs.mkdir 'source', ->
		console.log 'created source/ folder'
		Fs.mkdir 'source/javascripts', ->
			console.log 'created source/javascripts folder'
			Fs.mkdir 'source/stylesheets', ->
				console.log 'created source/stylesheets folder'
				Fs.writeFileSync 'source/index.html.jade', Stub.index
				Fs.writeFileSync 'source/layout.jade', Stub.layout
				Fs.writeFileSync 'source/javascripts/app.js.coffee', Stub.app
				Fs.writeFileSync 'source/stylesheets/site.css.styl', Stub.styl
				Fs.writeFileSync 'settings.json', Stub.settings
				console.log 'created settings.json'

