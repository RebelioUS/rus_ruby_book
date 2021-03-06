## File (файлы)

Подробное описание структуры файловой системы приведено в [приложении](appfile).

Файл - это фундаментальная абстракция в Linux. Linux придерживается философии "все есть файл", а значит большая часть взаимодействия программ реализуется через чтение и запись файлов. Операции с файлом осуществляются с помощью уникального файлового дескриптора. Основная часть системного программирования в Linux состоит в работе с файловыми дескрипторами.

Так как класс File, наследует класс IO, то с его экземплярами можно работать также, как если бы они были обычными потоками.

При открытии файла создается поток, позволяющий получить доступ к данным, сохраненным в файле.

Также в системе регистрируется файловый дескриптор - цифровой идентификатор открытого файла.

##### Константы:

`File::SEPARATOR` - символ, использующийся для разделения каталогов. В Windows обратная косая черта (`\`), а в Linux - косая черта (`/`);

`File::ALT_SEPARATOR` - альтернативный разделитель для каталогов;

`File::PATH_SEPARATOR` - символ, использующийся для разделения нескольких путей (":");

`File::NULL` - путь к нулевому устройству.

*****

`::new( name, mode, *perm ) # -> file`

Используется для открытия файла.

`::open( name, mode, *perm ) # -> 0`

`( name, mode, *perm )  { |file| } # -> object`

Версия предыдущего метода, принимающая блок, после выполнения которого файл закрывается.

### Взаимодействие с файловой системой

`::delete(*path) # -> integer`  
Синонимы: `unlink`

Используется для удаления указанных файлов. Возвращается количество удаленных файлов.

`::truncate( path, bytesize ) # -> 0`

Используется для уменьшения размера файла (в байтах).

`.truncate(bytesize) # -> 0`

Версия предыдущего метода, изменяющая размер текущего файла.

`::rename( path, new_name ) # -> 0`

Используется для переименования файла. Невозможность операции считается исключением.

`::symlink( path, name ) # -> 0`

Используется для создания символьной ссылки.

`::link( path, new_name ) # -> 0`

Используется для создания жесткой ссылки. Существование файла с тем же именем считается исключением.

`::utime( atime, mtime, *path ) # -> integer`

Используется для обновления времени последнего доступа и времени изменения. Возвращается количество измененных файлов.

`::readlink(path) # -> filename`

Используется для получения имени файла, связанного с символьной ссылкой.

`.flock(constants) # -> 0`

Используется для блокировки файлов. Блокировка необходима для ограничения одновременного доступа различных программ к одному файлу.

+ `File::LOCK_EX` - эксклюзивная блокировки (для записи). Доступ к файлу будет запрещен до закрытия файла текущей программой. Только одна программа может устанавливать такую блокировку;

  `File::LOCK_NB` - процесс выполнения не будет ожидать окончания блокировки файла;

  `File::LOCK_SH` - совместная блокировка файла (для чтения). Блокировка применяется при чтении информации из файла несколькими программами одновременно. Произвольное число программ могут устанавливать такую блокировку;

  `File::LOCK_UN` - отмена блокировки.

### Путь к файлу

`__FILE__` - имя выполняемого файла;

`__dir__` - путь к текущему каталогу (ruby 2.0).  
Аналогично `File.dirname __FILE__`.

`::path(object) # -> path`

Используется для получения пути к объекту с помощью метода `.to_path`. Отсутствие метода считается исключением.

`.path # -> path`  
Синонимы: `to_path`

Путь к файлу (переданный при его открытии).

`::absolute_path( filename, basedir = File::pwd ) # -> path`

Абсолютный путь. Тильда считается частью имени каталога.

~~~~~ ruby
  File.expand_path "test.rb", "~"
  # -> "/home/krugloff/~/test.rb"
  File.expand_path "/test.rb", ".rb" # -> "/test.rb"
~~~~~

`::expand_path( filename, basedir = File::pwd ) # -> path`

Абсолютный путь. Тильда соответствует домашнему каталогу.

~~~~~ ruby
  File.expand_path "test.rb", "~" # -> "/home/krugloff/test.rb"
  File.expand_path "/test.rb", ".rb" # -> "/test.rb"
  File.expand_path('../config/environment', "my_app/config.ru")
  # -> "/home/mak/my_app/config/environment"
~~~~~

`::realdirpath( filename, basedir = File::pwd ) # -> path`

Абсолютный путь. Тильда считается частью имени каталога. Отсутствие каталога считается исключением. При этом существование файла не проверяется.

~~~~~ ruby
  File.realdirpath "test.rb", "~" # -> error!
  File.realdirpath "/test.rb", ".rb" # -> "/test.rb"
~~~~~

`::realpath( filename, basedir = File::pwd ) # -> path`

Абсолютный путь. Тильда считается частью имени каталога. Отсутствие файла считается исключением.

~~~~~ ruby
  File.realpath "test.rb", "~" # -> error!
  File.realpath "/test.rb", ".rb" # -> error!
~~~~~

`::basename( path, extname = nil ) # -> filename`

Используется для получения имени файла из переданного пути (для разделения каталогов должна использоваться косая черта). Необязательный суффикс будет удален из результата.  
`File.basename "/test.rb", ".rb" # -> "test"`

`::dirname(path) # -> basedir`

Используется для получения базового каталога из переданного пути (для разделения каталогов должна использоваться косая черта).  
`File.dirname "/test.rb" # -> "/"`

`::split(path) # -> array`

Используется для разделения пути на путь к каталогу и имя файла.  
`File.split "/test.rb" # -> [ "/", "test.rb" ]`

`::extname(path) # -> extname`

Используется для получения расширения файла. Для каталогов возвращается пустой текст.  
`File.extname "/test.rb" # -> ".rb"`

`::join(*name) # -> path`

Используется для создания пути. Аргументы разделяются с помощью `File::SEPARATOR`.  
`File.join "/", "test", ".rb" # -> "/test/.rb"`

`::fnmatch?( pattern, path, constants = nil )`  
Синонимы: `fnmatch`

Проверка соответствия пути переданному [образцу](appfile).

~~~~~ ruby
  File.fnmatch? 'cat', 'cat'      # -> true
  File.fnmatch? 'cat', 'category' # -> false
~~~~~

~~~~~ ruby
  File.fnmatch? 'c{at,ub}s', 'cats'                    # -> false
  File.fnmatch? 'c{at,ub}s', 'cats', File::FNM_EXTGLOB # -> true [Ruby 2.0]
~~~~~

~~~~~ ruby
  File.fnmatch? 'c?t',     'cat'     # -> true
  File.fnmatch? 'c??t',    'cat'     # -> false
  File.fnmatch? 'c*',      'cats'    # -> true
  File.fnmatch? 'c*t',     'c/a/b/t' # -> true
  File.fnmatch? 'ca[a-z]', 'cat'     # -> true
  File.fnmatch? 'ca[^t]',  'cat'     # -> false
~~~~~

~~~~~ ruby
  File.fnmatch? 'cat', 'CAT'                     # -> false
  File.fnmatch? 'cat', 'CAT', File::FNM_CASEFOLD # -> true
~~~~~

~~~~~ ruby
  File.fnmatch? '?',   '/', File::FNM_PATHNAME   # -> false
  File.fnmatch? '*',   '/', File::FNM_PATHNAME   # -> false
  File.fnmatch? '[/]', '/', File::FNM_PATHNAME   # -> false
~~~~~

~~~~~ ruby
  File.fnmatch? '\?',   '?'                       # -> true
  File.fnmatch? '\a',   'a'                       # -> true
  File.fnmatch? '\a',   '\a', File::FNM_NOESCAPE  # -> true
  File.fnmatch? '[\?]', '?'                       # -> true
~~~~~

~~~~~ ruby
  File.fnmatch? '*',   '.profile'                      # -> false
  File.fnmatch? '*',   '.profile', File::FNM_DOTMATCH  # -> true
  File.fnmatch? '.*',  '.profile'                      # -> true
~~~~~

~~~~~ ruby
  rbfiles = '**/*.rb'
  File.fnmatch? rbfiles, 'main.rb'                    # -> false
  File.fnmatch? rbfiles, './main.rb'                  # -> false
  File.fnmatch? rbfiles, 'lib/song.rb'                # -> true
  File.fnmatch? '**.rb', 'main.rb'                    # -> true
  File.fnmatch? '**.rb', './main.rb'                  # -> false
  File.fnmatch? '**.rb', 'lib/song.rb'                # -> true
  File.fnmatch? '*',     'dave/.profile'              # -> true
~~~~~

~~~~~ ruby
  pattern = '*/*'
  File.fnmatch? pattern, 'dave/.profile', File::FNM_PATHNAME  # -> false
  File.fnmatch? pattern, 'dave/.profile',
    File::FNM_PATHNAME | File::FNM_DOTMATCH
  # -> true
~~~~~

~~~~~ ruby
  pattern = '**/foo'
  File.fnmatch? pattern, 'a/b/c/foo', File::FNM_PATHNAME     # -> true
  File.fnmatch? pattern, '/a/b/c/foo', File::FNM_PATHNAME    # -> true
  File.fnmatch? pattern, 'c:/a/b/c/foo', File::FNM_PATHNAME  # -> true
  File.fnmatch? pattern, 'a/.b/c/foo', File::FNM_PATHNAME    # -> false
  File.fnmatch? pattern, 'a/.b/c/foo',
    File::FNM_PATHNAME | File::FNM_DOTMATCH
  # -> true
~~~~~

`::identical?( path, path2 )`

Сравнение двух путей.

### Права доступа

#### Изменение ограничений

`::chmod( *perm, *path ) # -> integer`

Используется для изменения прав доступа. Возвращается количество измененных файлов.

`::lchmod( perm, *path ) # -> integer`

Версия предыдущего метода, изменяющая права доступа для символьных ссылок, а не для файлов, на которые они ссылаются.

`::chown( user, group, *path ) # -> integer`

Используется для изменения владельца файла и группы владельцев. Возвращается количество измененных файлов. Только администратор может изменить владельца файла. Текущий владелец может изменить группу на любую в которую входит. Владелец и группа устанавливаются с помощью их цифровых идентификаторов. Идентификаторы 0 и -1 игнорируются.

`::lchown( user, group, *path ) # -> integer`

Версия предыдущего метода, изменяющая данные для символьных ссылок, а не для файлов, на которые они ссылаются.

`::umask( mask = nil ) # -> old_perm`

Используется для изменения прав доступа, присваиваемых создаваемым файлам и каталогам. Переданное число вычитается из прав доступа, используемых по умолчанию, и результат используется для объявление новых ограничений.

#### Предикаты

`::readable?(path)`

Проверка возможности чтения на основе действующих идентификаторов.

`::readable_real?(path)`

Проверка возможности чтения на основе реальных идентификаторов.

`::world_readable?(path)`

Проверка возможности чтения файла всеми пользователями (возвращаются права доступа).

`::writable?(path)`

Проверка возможности записи на основе действующих идентификаторов.

`::writable_real?(path)`

Проверка возможности записи на основе реальных идентификаторов.

`::world_writable?(path)`

Проверка возможности записи файла всеми пользователями (возвращаются права доступа).

`::executable?(path)`

Проверка возможности выполнения на основе действующих идентификаторов.

`::executable_real?(path)`

Проверка возможности выполнения на основе реальных идентификаторов.

`::setuid?(path)`

Проверка возможности выполнения файла с правами владельца.

`::setgid?(path)`

Проверка возможности выполнения файла с правами группы владельцев.

`::sticky?(path)`

Проверка наличия дополнительного свойства (sticky bit) для каталогов.

`::owned?(path)`

Проверка равенства идентификатора владельца файла и действующего идентификатора (текущий пользователь является владельцем файла).

`::grpowned?(path)`

Проверка равенства цифрового идентификатора текущей группы и цифрового идентификатора группы владельцев файла (текущий пользователь относится к владельцам файла).

### Статистика

`::stat(path) # -> a_file_stat`

Используется для получения экземпляра класса File::Stat, содержащего информацию о файле.

`::lstat(path) # -> a_file_stat`

Версия предыдущего метода, сохраняющая данные для символьных ссылок, а не для файлов, на которые они ссылаются.

`.lstat # -> file_stat`

Версия предыдущего метода, возвращающая информацию о текущем файле.

`::atime(path) # -> time`

Время последнего доступа к файлу.

`.atime # -> time`

Время последнего доступа к текущему файлу.

`::ctime(path) # -> time`

Время последнего изменения информации о файле (но не самого файла).

`.ctime # -> time`

Время последнего изменения информации о текущем файле (но не самого файла).

`::mtime(path) # -> time`

Время последнего изменения файла.

`.mtime # -> time`

Время последнего изменения текущего файла.

`::size(path) # -> integer`

Размер файла.

`.size # -> integer`

Размер текущего файла.

`::ftype(path) # -> string`

Тип файла.

+ _"file"_ - обычный файл;  
  _"directory"_ - каталог;  
  _"blockSpecial"_ - блочное устройство;  
  _"characterSpecial"_ - символьное устройство;  
  _"fifo"_ - конвейер;  
  _"link"_ - ссылка;  
  _"socket"_ - сокет;  
  _"unknown"_ - неизвестный тип.

### Предикаты

`::exist?(path)`  
Синонимы: `exists?`

Проверка существования файла.

`::size?(path) # -> integer`

Проверка существования файла. Для существующих файлов возвращается их размер.

`::zero?(path)`

Проверка существования файла нулевого размера.

`::file?(path)`

Проверка существования обычного файла.

`::symlink?(path)`

Проверка существования символьной ссылки.

`::directory?(path)`

Проверка существования каталога.

`::blockdev?(path)`

Проверка существования блочного устройства.

`::chardev?(path)`

Проверка существования символьного устройства.

`::pipe?(path)`

Проверка существования конвейера.

`::socket?(path)`

Проверка существования сокета.
