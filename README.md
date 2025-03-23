# ytarchive

Attempt to archive a given Youtube livestream from the start. This is most useful for streams that have already started and you want to download, but can also be used to wait for a scheduled stream and start downloading as soon as it starts. If you want to download a VOD, I recommend [yt-dlp](https://github.com/yt-dlp/yt-dlp), which is an actively maintained fork of youtube-dl with more features.

## Dependencies

- [FFmpeg](https://ffmpeg.org/) needs to be installed to mux the final file.

## Installation

Download the latest pre-release from [the releases page](https://github.com/Kethsar/ytarchive/releases)

Alternatively, if you have Go properly installed and set up, run `go install github.com/Kethsar/ytarchive@dev`

se usar windows:
- baixa o arquivo ytarchive_windows_amd64.zip e descompacta numa pasta
- abre um terminal nessa pasta
- entra com o comando de terminal:
ytarchive --threads 8 cole_a_url_do_video_aqui 

enjoy

## Usage

```
usage: ytarchive [OPTIONS] [url] [quality]

	[url] is a youtube livestream URL. If not provided, you will be
	prompted to enter one.

	[quality] is a slash-delimited list of video qualities you want
	to be selected for download, from most to least wanted. If not
	provided, you will be prompted for one, with a list of available
	qualities to choose from. The following values are valid:
	audio_only, 144p, 240p, 360p, 480p, 720p, 720p60, 1080p, 1080p60, 1440p, 1440p60, 2160p, 2160p60, best

Options:
	-h
	--help
		Show this help message.

	-4
	--ipv4
		Make all connections using IPv4.

	-6
	--ipv6
		Make all connections using IPv6.

	--info-only
		Print stream information such as Video title, Selected quality
		Stream start time and duration and then exits.

	--add-metadata
		Write some basic metadata information to the final file.

	--audio-url GOOGLEVIDEO_URL
		Pass in the given url as the audio fragment url. Must be a
		Google Video url with an itag parameter of 140.

	--capture-duration DURATION or TIMESTRING
		Captures a livestream for the specified length of time 
		and then exits and finalizes the video.
		Supports time durations (e.g. 1d8h10m) or time strings (e.g. 12:30:05).

	-c
	--cookies COOKIES_FILE
		Give a cookies.txt file that has your youtube cookies. Allows
		the script to access members-only content if you are a member
		for the given stream's user. Must be netscape cookie format.

	--debug
		Print a lot of extra information.

	--disable-save-state
		Disable saving state for resumable downloads. Useful if you are
		archiving the same stream multiple times in the same directory
		for some reason.

	-dp
	--directory-permissions PERMISSIONS
		Set the filesystem permissions for created directories. Uses unix
		numeric notation. Be aware of umask settings for your directory.
		Default is 0755.

	--error
		Print only errors and general information.

	--ffmpeg-path FFMPEG_PATH
		Set a specific ffmpeg location, including program name.
		e.g. "C:\ffmpeg\ffmpeg.exe" or "/opt/ffmpeg/ffmpeg"

	-fp
	--file-permissions PERMISSIONS
		Set the filesystem permissions for created files. Uses unix
		numeric notation. Be aware of umask settings for your directory.
		Default is 0644.

	--h264
		Only download h264 video, skipping VP9 if it would have been used.

	-k
	--keep-ts-files
		Keep the final stream audio and video files after muxing them
		instead of deleting them.

	-l
	--lookalike-chars
		Use lookalikes for forbidden characters in the filename output format.
		Emulates forbidden characters by using the same replacement characters as yt-dlp.
		This will make the filenames look closer to the original titles.

	--members-only
		Only download members-only streams. Can only be used with channel URLs
		such as /live, /streams, etc, and requires cookies.
		Useful when monitoring channels and you only want membership streams.

	--merge
		Automatically run the ffmpeg command for the downloaded streams
		when manually cancelling the download. You will be prompted otherwise.

	--metadata KEY=VALUE
		If writing metadata, overwrite/add metadata key-value entry.
		KEY is a metadata key that ffmpeg recognizes. If invalid, ffmpeg may ignore it or error.
		VALUE is a format template. If empty string (''), omit writing metadata for the key.
		See FORMAT TEMPLATE OPTIONS below for a list of available format keys.
		Can be used multiple times.

	--mkv
		Mux the final file into an mkv container instead of an mp4 container.
		Ignored when downloading audio only.

	--monitor-channel
		Continually monitor a channel for streams. Requires using a /live URL.
		This will go back to checking for a stream after it finishes downloading
		the current one. Implies '-r 60 --merge' unless set separately. Minimum
		30 second wait time, 60 or more recommended. Using 'best' for quality or
		setting a decently exhaustive list recommended to prevent waiting for
		input if selected quality is not available for certain streams.
		Be careful to monitor your disk usage when using this to avoid filling
		your drive while away.

	--newline
		Print every message to a new line, instead of some messages reusing one
		line.

	--no-audio
		Do not download the audio stream

	--no-frag-files
		Keep fragment data in memory instead of writing to an intermediate file.
		This has the possibility to drastically increase RAM usage if a fragment
		downloads particularly slowly as more fragments after it finish first.
		This is only an issue when --threads >1
		Highly recommended if you don't have strict RAM limitations. Especially
		on Wangblows, which has caused issues with file locking when trying to
		delete fragment files.

	--no-merge
		Do not run the ffmpeg command for the downloaded streams
		when manually cancelling the download. You will be prompted otherwise.

	--no-save
		Do not save any downloaded data and files if not having ffmpeg
		run when manually cancelling the download. You will be prompted otherwise.
		Does nothing if --merge is set.

	--no-save-state
		Do not leave files required for resuming downloads when manually
		cancelling the download. You will be prompted otherwise.
		Does nothing if --merge or --save are set.

	--no-video
		If a googlevideo url is given or passed with --audio-url, do not
		prompt for a video url. If a video url is given with --video-url
		then this is effectively ignored.

	-n
	--no-wait
		Do not wait for a livestream if it's a future scheduled stream.

	-o
	--output FILENAME_FORMAT
		Set the output file name EXCLUDING THE EXTENSION. Can include
		formatting similar to youtube-dl, albeit much more limited.
		See FORMAT OPTIONS below for a list of available format keys.
		Default is '%(title)s-%(id)s'

	--potoken <PO TOKEN>
		PO Token from your browser, basically required along with cookies these days.
		Refer to https://github.com/yt-dlp/yt-dlp/wiki/Extractors#po-token-guide

	--proxy <SCHEME>://[<USER>:<PASS>@]<HOST>:<PORT>
		Specify a proxy to use for downloading. e.g.
			- socks5://127.0.0.1:1080
			- http://192.168.1.1:8080
			- http://user:password@proxy.example.com:8080

		HTTP, HTTPS and SOCKS5 proxy servers are supported.

	-q
	--quiet
		Print nothing to the console except information relevant for user input.

	--retry-frags ATTEMPTS
		Set the number of attempts to make when downloading a stream fragment.
		Set to 0 to retry indefinitely, or until we are completely unable to.
		Default is 10.

	-r
	--retry-stream SECONDS
		If waiting for a scheduled livestream, re-check if the stream is
		up every SECONDS instead of waiting for the initial scheduled time.
		If SECONDS is less than the poll delay youtube gives (typically
		15 seconds), then this will be set to the value youtube provides.

	--save
		Automatically save any downloaded data and files if not having
		ffmpeg run when manually cancelling the download. You will be prompted
		otherwise. Does nothing if --merge is set.

	--save-state
		Automatically leave files alone and do not delete anything when manually
		cancelling the download, allowing for resuming a download later when
		possible. You will be prompted otherwise.
		Resuming requires the stream be available to download as normal.
		Does nothing if --merge or --save are set.

	--separate-audio
		Save the audio to a separate file, similar to when downloading
		audio_only, alongside the final muxed file. This includes embedding
		metadata and the thumbnail if set.

	--start-delay DURATION or TIMESTRING
		Waits for a specified length of time before starting to capture a stream from that time.
		Supports time durations (e.g. 1d8h10m) or time strings (e.g. 12:30:05).
		
		Note: * NOT supported when using also using '--live-from'.
		      * If the stream is scheduled and has not yet begun then
		        the delay does not start counting until the stream has begun.
		      * Ignored when resuming a download.

	-td
	--temporary-dir DIRECTORY
		Set the working directory for the download. This is where the
		temporary files will be stored. If not set, the output directory
		will be used.

	--threads THREAD_COUNT
		Set the number of threads to use for downloading audio and video
		fragments. The total number of threads running will be
		THREAD_COUNT * 2 + 3. Main thread, a thread for each audio and
		video download, and THREAD_COUNT number of fragment downloaders
		for both audio and video.
		
		Setting this to a large number has a chance at causing the download
		to start failing with HTTP 401. Restarting the download with a smaller
		thread count until you no longer get 401s should work. Default is 1.

	-t
	--thumbnail
		Download and embed the stream thumbnail in the finished file.
		Whether the thumbnail shows properly depends on your file browser.
		Windows' seems to work. Nemo on Linux seemingly does not.

	--trace
		Print just about any information that might have reason to be printed.
		Very spammy, do not use this unless you have good reason.

	-v
	--verbose
		Print extra information.

	-V
	--version
		Print the version number and exit.

	--video-url GOOGLEVIDEO_URL
		Pass in the given url as the video fragment url. Must be a
		Google Video url with an itag parameter that is not 140.

	--vp9
		If there is a VP9 version of your selected video quality,
		download that instead of the usual h264.

	-w
	--wait
		Wait for a livestream if it's a future scheduled stream.
		If this option is not used when a scheduled stream is provided,
		you will be asked if you want to wait or not.

	--warn
		Print warning, errors, and general information. This is the default log
		level.

	--write-description
		Write the video description to a separate .description file.
	
	--write-mux-file
		Write the ffmpeg command that would mux audio and video or put audio
		into an mp4 container instead of running the command automatically.
		Useful if you want to tweak the command, want a higher log level, etc.

	--write-thumbnail
		Write the thumbnail to a separate file.

	--live-from DURATION, TIMESTRING or NOW
		Starts the download from the specified time in the future, the past or 'now'.
		Use a negative time value to skip back in time from now.
		Use a positive time value to specify the timestamp in the stream to start 
		capturing from (from the start of the stream).

		Supports time durations (e.g. 1d8h30m5s) or time strings (e.g. 32:30:05).
		Examples: * '--live-from -01:10:00' will seek backwards 1 hour and 10 minutes from now
					and then start downloading from that time.
		          * '--live-from 1h10mm00s' will begin downloading from 1 hour 10 minutes 
				    after the stream started.
		          * '--live-from now' will start recording from the current stream time.

Examples:
	ytarchive -w
		Waits for a stream. Will prompt for a URL and quality.

	ytarchive -w https://www.youtube.com/watch?v=CnWDmKx9cQQ 1080p60/best
		Waits for the given stream URL. Will prioritize downloading in 1080p60.
		If 1080p60 is not an available quality, it will choose the best of what
		is available.

	ytarchive --threads 3 https://www.youtube.com/watch?v=ZK1GXnz-1Lw best
		Downloads the given stream with 3 threads in the best available quality.
		Will ask if you want to wait if the stream is scheduled but not started.

	ytarchive -r 30 https://www.youtube.com/channel/UCZlDXzGoo7d44bwdNObFacg/live best
		Will wait for a livestream at the given URL, checking every 30 seconds.

	ytarchive -c cookies-youtube-com.txt https://www.youtube.com/watch?v=_touw1GND-M best
		Loads the given cookies file and attempts to download the given stream.
		Will ask if you want to wait.

	ytarchive --no-wait --add-metadata https://www.youtube.com/channel/UCvaTdHTWBGv3MKj3KVqJVCw/live best
		Attempts to download the given stream, and will add metadata to the
		final muxed file. Will not wait if there is no stream or if it has not
		started.

	ytarchive -o '%(channel)s/%(upload_date)s_%(title)s' https://www.youtube.com/watch?v=HxV9UAMN12o best
		Download the given stream to a directory with the channel name, and a
		file that will have the upload date and stream title. Will prompt to
		wait.

	ytarchive -w -k -t --vp9 --merge --no-frag-files https://www.youtube.com/watch?v=LE8V5iNemBA best
		Waits, keeps the final .ts files, embeds the stream thumbnail, merges
		the downloaded files if download is stopped manually, and keeps
		fragments in memory instead of writing to intermediate files.
		Downloads the stream video in VP9 if available. This set of flags will
		not require any extra user input if something goes wrong.

	ytarchive -k -t --vp9 --monitor-channel --no-frag-files https://www.youtube.com/channel/UCvaTdHTWBGv3MKj3KVqJVCw/live best
		Same as above, but waits for a stream on the given channel, and will
		repeat the cycle after downloading each stream.

	ytarchive --proxy http://127.0.0.1:9050 https://www.youtube.com/watch?v=2aIdHTuyYMA best
		Downloads the given stream with a local HTTP proxy.

FORMAT TEMPLATE OPTIONS
	Format template keys provided are made to be the same as they would be for
	youtube-dl. See https://github.com/ytdl-org/youtube-dl#output-template

	For file names, each template substitution is sanitized by replacing invalid file name
	characters with an underscore (_). If '--lookalike-chars' is used, invalid file name
	characters get replaced by the same lookalike characters that yt-dlp uses instead.

	id (string): Video identifier
	url (string): Video URL
	title (string): Video title
	channel_id (string): ID of the channel
	channel (string): Full name of the channel the livestream is on
	upload_date (string: YYYYMMDD): Technically stream start date, UTC timezone - see note below
	start_date (string: YYYYMMDD): Stream start date, UTC timezone
	publish_date (string: YYYYMMDD): Stream publish date, UTC timezone
	description (string): Video description [disallowed for file name format template]

	Note on upload_date: rather than the actual upload date, stream start date is used to
	provide a better default date for youtube-dl output templates that use upload_date.
	To get the actual upload date, publish date seems to be the same as upload date for streams.
```
##TRADUÇÃO PARA PORTUGUES: 
```
uso: ytarchive [OPÇÕES] [url] [qualidade]

[url] é uma URL de transmissão ao vivo do YouTube. Se não for fornecida, você será
solicitado a inserir uma.

[qualidade] é uma lista delimitada por barras de qualidades de vídeo que você deseja
que sejam selecionadas para download, da mais para a menos desejada. Se não
fornecida, você será solicitado a inserir uma, com uma lista de qualidades
disponíveis para escolher. Os seguintes valores são válidos:
audio_only, 144p, 240p, 360p, 480p, 720p, 720p60, 1080p, 1080p60, 1440p, 1440p60, 2160p, 2160p60, melhor
Opções:
	-h
	--ajuda
		Mostrar esta mensagem de ajuda.

	-4
	--ipv4
		Faça todas as conexões usando IPv4.

	-6
	--ipv6
		Faça todas as conexões usando IPv6.

	--somente informação
		Imprimir informações de fluxo, como título do vídeo, qualidade selecionada
		Horário de início e duração da transmissão e depois saída.

	--adicionar-metadados
		Escreva algumas informações básicas de metadados no arquivo final.

	--url de áudio GOOGLEVIDEO_URL
		Passe a url fornecida como a url do fragmento de áudio. Deve ser um
		URL do Google Video com parâmetro itag de 140.

	--capture-duration DURAÇÃO ou TIMESTRING
		Captura uma transmissão ao vivo pelo período de tempo especificado
		e então sai e finaliza o vídeo.
		Suporta durações de tempo (por exemplo, 1d8h10m) ou sequências de tempo (por exemplo, 12:30:05).

	-c
	--cookies ARQUIVO_DE_COOKIES
		Dê um arquivo cookies.txt que tenha seus cookies do YouTube. Permite
		o script para acessar conteúdo exclusivo para membros se você for um membro
		para o usuário do fluxo fornecido. Deve ser o formato de cookie do netscape.

	--depurar
		Imprima muitas informações extras.

	--desabilitar-estado-de-salvamento
		Desabilite o salvamento de estado para downloads retomáveis. Útil se você estiver
		arquivando o mesmo fluxo várias vezes no mesmo diretório
		por algum motivo.

	-dp
	--directory-permissions PERMISSÕES
		Defina as permissões do sistema de arquivos para diretórios criados. Usa unix
		notação numérica. Esteja ciente das configurações umask para seu diretório.
		O padrão é 0755.

	--erro
		Imprima somente erros e informações gerais.

	--ffmpeg-caminho FFMPEG_PATH
		Defina um local específico do ffmpeg, incluindo o nome do programa.
		por exemplo "C:\ffmpeg\ffmpeg.exe" ou "/opt/ffmpeg/ffmpeg"

	-fp
	--file-permissions PERMISSÕES
		Defina as permissões do sistema de arquivos para os arquivos criados. Usa unix
		notação numérica. Esteja ciente das configurações umask para seu diretório.
		O padrão é 0644.

	--h264
		Baixe somente vídeos h264, ignorando o VP9 caso ele tenha sido usado.

	-k
	--manter-arquivos-ts
		Mantenha os arquivos de áudio e vídeo do fluxo final após a mixagem
		em vez de excluí-los.

	-eu
	--caracteres semelhantes
		Use sósias para caracteres proibidos no formato de saída do nome do arquivo.
		Emula caracteres proibidos usando os mesmos caracteres de substituição do yt-dlp.
		Isso fará com que os nomes dos arquivos pareçam mais próximos dos títulos originais.

	--somente para membros
		Baixe apenas streams exclusivos para membros. Só pode ser usado com URLs de canais
		como /live, /streams, etc, e requer cookies.
		Útil ao monitorar canais e você deseja apenas fluxos de membros.

	--mesclar
		Executar automaticamente o comando ffmpeg para os fluxos baixados
		ao cancelar manualmente o download. Você será avisado do contrário.

	--metadados CHAVE=VALOR
		Ao gravar metadados, substitua/adicione a entrada de chave-valor dos metadados.
		KEY é uma chave de metadados que o ffmpeg reconhece. Se for inválido, o ffmpeg pode ignorá-lo ou dar erro.
		VALUE é um modelo de formato. Se string vazia (''), omita metadados de escrita para a chave.
		Veja OPÇÕES DE MODELO DE FORMATO abaixo para obter uma lista de chaves de formato disponíveis.
		Pode ser usado várias vezes.

	--mkv
		Multiplique o arquivo final em um contêiner mkv em vez de um contêiner mp4.
		Ignorado ao baixar somente áudio.

	--canal-monitor
		Monitore continuamente um canal para streams. Requer usar uma URL /live.
		Isso retornará para verificar se há um fluxo após o término do download
		o atual. Implica '-r 60 --merge' a menos que definido separadamente. Mínimo
		Tempo de espera de 30 segundos, 60 ou mais recomendado. Usar "melhor" para qualidade ou
		estabelecendo uma lista decentemente exaustiva recomendada para evitar a espera por
		entrada se a qualidade selecionada não estiver disponível para determinados fluxos.
		Tenha cuidado ao monitorar o uso do disco ao usar isso para evitar encher
		sua viagem enquanto estiver fora.

	--nova linha
		Imprima cada mensagem em uma nova linha, em vez de algumas mensagens reutilizarem uma
		linha.

	--sem-áudio
		Não baixe o fluxo de áudio

	--no-frag-files
		Mantenha os dados do fragmento na memória em vez de gravá-los em um arquivo intermediário.
		Isso tem a possibilidade de aumentar drasticamente o uso de RAM se um fragmento
		downloads particularmente lentos, pois mais fragmentos depois que ele termina primeiro.
		Isso só é um problema quando --threads >1
		Altamente recomendado se você não tem limitações estritas de RAM. Especialmente
		no Wangblows, o que causou problemas com bloqueio de arquivo ao tentar
		excluir arquivos de fragmentos.

	--não mesclar
		Não execute o comando ffmpeg para os fluxos baixados
		ao cancelar manualmente o download. Você será avisado do contrário.

	--não-salvar
		Não salve nenhum dado ou arquivo baixado se não tiver o ffmpeg
		executar ao cancelar manualmente o download. Você será avisado do contrário.
		Não faz nada se --merge estiver definido.

	--sem-estado-de-salvamento
		Não deixe arquivos necessários para retomar os downloads quando manualmente
		cancelando o download. Você será avisado do contrário.
		Não faz nada se --merge ou --save estiverem definidos.

	--sem-vídeo
		Se uma URL do Google Video for fornecida ou passada com --audio-url, não
		prompt para uma url de vídeo. Se uma url de vídeo for fornecida com --video-url
		então isso é efetivamente ignorado.

	-n
	--não espere
		Não espere por uma transmissão ao vivo se ela for uma transmissão futura agendada.

	-o
	--output FORMATO_DO_NOME_DO_ARQUIVO
		Defina o nome do arquivo de saída EXCLUINDO A EXTENSÃO. Pode incluir
		formatação semelhante ao youtube-dl, embora muito mais limitada.
		Veja OPÇÕES DE FORMATO abaixo para obter uma lista de chaves de formato disponíveis.
		O padrão é '%(title)s-%(id)s'

	--potoken <TOKEN PO>
		Token PO do seu navegador, basicamente necessário junto com os cookies atualmente.
		Consulte https://github.com/yt-dlp/yt-dlp/wiki/Extractors#po-token-guide

	--proxy <ESQUEMA>://[<USUÁRIO>:<SENHA>@]<HOST>:<PORTA>
		Especifique um proxy a ser usado para download. por exemplo
			- meias5://127.0.0.1:1080
			- http://192.168.1.1:8080
			- http://usuário:senha@proxy.exemplo.com:8080

		Servidores proxy HTTP, HTTPS e SOCKS5 são suportados.

	-q
	--quieto
		Não imprima nada no console, exceto informações relevantes para a entrada do usuário.

	--retry-frags TENTATIVAS
		Defina o número de tentativas a serem feitas ao baixar um fragmento de fluxo.
		Defina como 0 para tentar novamente indefinidamente ou até que não consigamos mais.
		O padrão é 10.

	-r
	--retry-stream SEGUNDOS
		Se estiver esperando por uma transmissão ao vivo agendada, verifique novamente se a transmissão está
		a cada SEGUNDOS em vez de esperar pelo horário inicial agendado.
		Se SECONDS for menor que o atraso da pesquisa fornecido pelo YouTube (normalmente
		15 segundos), então este será definido como o valor fornecido pelo YouTube.

	--salvar
		Salvar automaticamente todos os dados e arquivos baixados se não tiver
		ffmpeg executado ao cancelar manualmente o download. Você será solicitado
		caso contrário. Não faz nada se --merge estiver definido.

	--salvar-estado
		Deixe os arquivos sozinhos automaticamente e não exclua nada quando manualmente
		cancelando o download, permitindo retomar o download mais tarde quando
		possível. Você será avisado de outra forma.
		Para retomar, é necessário que o stream esteja disponível para download normalmente.
		Não faz nada se --merge ou --save estiverem definidos.

	--áudio separado
		Salve o áudio em um arquivo separado, semelhante ao download
		audio_only, junto com o arquivo final multiplexado. Isso inclui a incorporação
		metadados e a miniatura, se definida.

	--start-delay DURAÇÃO ou TIMESTRING
		Aguarda um período de tempo especificado antes de começar a capturar um fluxo daquele momento.
		Suporta durações de tempo (por exemplo, 1d8h10m) ou sequências de tempo (por exemplo, 12:30:05).
		
		Nota: * NÃO suportado ao usar também '--live-from'.
		* Se a transmissão estiver agendada e ainda não tiver começado, então
		o atraso não começa a ser contado até que a transmissão tenha começado.
		* Ignorado ao retomar um download.

	-td
	--temporary-dir DIRETÓRIO
		Defina o diretório de trabalho para o download. É aqui que o
		arquivos temporários serão armazenados. Se não for definido, o diretório de saída
		será usado.

	--threads CONTAGEM_DE_FIOS
		Defina o número de threads a serem usados para baixar áudio e vídeo
		fragmentos. O número total de threads em execução será
		THREAD_COUNT * 2 + 3. Tópico principal, um tópico para cada áudio e
		download de vídeo e THREAD_COUNT número de baixadores de fragmentos
		tanto para áudio quanto para vídeo.
		
		Definir isso como um número grande pode causar o download
		para começar a falhar com HTTP 401. Reiniciando o download com um menor
		contagem de threads até que você não tenha mais 401s deve funcionar. O padrão é 1.

	-t
	--miniatura
		Baixe e incorpore a miniatura do fluxo no arquivo finalizado.
		Se a miniatura será exibida corretamente depende do seu navegador de arquivos.
		O Windows parece funcionar. O Nemo no Linux aparentemente não.

	--traço
		Imprima praticamente qualquer informação que possa ter motivo para ser impressa.
		Muito spam, não use isso a menos que tenha um bom motivo.

	-v
	--detalhado
		Imprima informações extras.

	-V
	--versão
		Imprima o número da versão e saia.

	--vídeo-url GOOGLEVIDEO_URL
		Passe a url fornecida como a url do fragmento de vídeo. Deve ser um
		URL do Google Video com um parâmetro itag diferente de 140.

	--vp9
		Se houver uma versão VP9 da qualidade de vídeo selecionada,
		baixe isso em vez do h264 usual.

	-c
	--espere
		Aguarde uma transmissão ao vivo se ela for uma transmissão futura agendada.
		Se esta opção não for usada quando um fluxo agendado for fornecido,
		você será perguntado se deseja esperar ou não.

	--avisar
		Imprimir aviso, erros e informações gerais. Este é o log padrão
		nível.

	--escrever-descrição
		Escreva a descrição do vídeo em um arquivo .description separado.
	
	--escrever-arquivo-mux
		Escreva o comando ffmpeg que irá mixar áudio e vídeo ou colocar áudio
		em um contêiner mp4 em vez de executar o comando automaticamente.
		Útil se você quiser ajustar o comando, quiser um nível de log mais alto, etc.

	--escrever-miniatura
		Grave a miniatura em um arquivo separado.

	--ao vivo-de DURAÇÃO, CADEIA DE TEMPO ou AGORA
		Inicia o download a partir do momento especificado no futuro, no passado ou 'agora'.
		Use um valor de tempo negativo para voltar no tempo a partir de agora.
		Use um valor de tempo positivo para especificar o registro de data e hora no fluxo para iniciar
		capturando de (desde o início do fluxo).

		Suporta durações de tempo (por exemplo, 1d8h30m5s) ou sequências de tempo (por exemplo, 32:30:05).
		Exemplos: * '--live-from -01:10:00' buscará para trás 1 hora e 10 minutos a partir de agora
					e então começar a baixar a partir desse momento.
		* '--live-from 1h10mm00s' começará a baixar a partir de 1 hora e 10 minutos
				depois que a transmissão começou.
		* '--live-from now' iniciará a gravação a partir do horário atual da transmissão.

Exemplos:
	ytarquivo -w
		Aguarda um fluxo. Solicitará uma URL e qualidade.

	ytarchive -w https://www.youtube.com/watch?v=CnWDmKx9cQQ 1080p60/melhor
		Aguarda a URL de transmissão fornecida. Priorizará o download em 1080p60.
		Se 1080p60 não for uma qualidade disponível, ele escolherá a melhor das opções
		está disponível.

	ytarchive --threads 3 https://www.youtube.com/watch?v=ZK1GXnz-1Lw melhor
		Baixa o fluxo fornecido com 3 threads na melhor qualidade disponível.
		Perguntará se você deseja esperar caso a transmissão esteja agendada, mas não tenha iniciado.

	ytarchive -r 30 https://www.youtube.com/channel/UCZlDXzGoo7d44bwdNObFacg/live melhor
		Aguardará uma transmissão ao vivo no URL fornecido, verificando a cada 30 segundos.

	ytarchive -c cookies-youtube-com.txt https://www.youtube.com/watch?v=_touw1GND-M melhor
		Carrega o arquivo de cookies fornecido e tenta baixar o fluxo fornecido.
		Perguntarei se você quer esperar.

	ytarchive --no-wait --add-metadata https://www.youtube.com/channel/UCvaTdHTWBGv3MKj3KVqJVCw/live melhor
		Tenta baixar o fluxo fornecido e adicionará metadados ao
		arquivo final multiplexado. Não esperará se não houver fluxo ou se não tiver
		iniciado.

	ytarchive -o '%(channel)s/%(upload_date)s_%(title)s' https://www.youtube.com/watch?v=HxV9UAMN12o melhor
		Baixe o fluxo fornecido para um diretório com o nome do canal e um
		arquivo que terá a data de upload e o título do fluxo. Solicitará
		espere.

	ytarchive -w -k -t --vp9 --merge --no-frag-files https://www.youtube.com/watch?v=LE8V5iNemBA melhor
		Espera, mantém os arquivos .ts finais, incorpora a miniatura do fluxo, mescla
		os arquivos baixados se o download for interrompido manualmente e mantido
		fragmentos na memória em vez de gravar em arquivos intermediários.
		Baixa o vídeo do stream em VP9 se disponível. Este conjunto de sinalizadores irá
		não requer nenhuma entrada extra do usuário caso algo dê errado.

	ytarchive -k -t --vp9 --monitor-channel --no-frag-files https://www.youtube.com/channel/UCvaTdHTWBGv3MKj3KVqJVCw/live melhor
		O mesmo que acima, mas espera por um fluxo no canal fornecido e irá
		repita o ciclo após baixar cada fluxo.

	ytarchive --proxy http://127.0.0.1:9050 https://www.youtube.com/watch?v=2aIdHTuyYMA melhor
		Baixa o fluxo fornecido com um proxy HTTP local.

OPÇÕES DE MODELO DE FORMATO
	As chaves de modelo de formato fornecidas são feitas para serem as mesmas que seriam para
	youtube-dl. Veja https://github.com/ytdl-org/youtube-dl#output-template

	Para nomes de arquivo, cada substituição de modelo é higienizada pela substituição de nome de arquivo inválido
	caracteres com um sublinhado (_). Se '--lookalike-chars' for usado, nome de arquivo inválido
	os caracteres são substituídos pelos mesmos caracteres semelhantes que o yt-dlp usa.

	id (string): Identificador de vídeo
	url (string): URL do vídeo
	título (string): Título do vídeo
	channel_id (string): ID do canal
	canal (string): Nome completo do canal onde a transmissão ao vivo está
	upload_date (string: AAAAMMDD): Tecnicamente, data de início do fluxo, fuso horário UTC - veja a nota abaixo
	start_date (string: AAAAMMDD): Data de início do fluxo, fuso horário UTC
	publish_date (string: AAAAMMDD): Data de publicação do fluxo, fuso horário UTC
	descrição (string): Descrição do vídeo [não permitido para modelo de formato de nome de arquivo]

	Nota sobre upload_date: em vez da data de upload real, a data de início do fluxo é usada para
	forneça uma data padrão melhor para modelos de saída do youtube-dl que usam upload_date.
	Para obter a data de upload real, a data de publicação parece ser a mesma que a data de upload dos streams.
```

```
