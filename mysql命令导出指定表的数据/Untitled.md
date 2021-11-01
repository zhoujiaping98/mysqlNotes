### Postgresql 学习笔记

To have launchd start postgresql now and restart at login:

 brew services start postgresql

Or, if you don't want/need a background service you can just run:

 pg_ctl -D /opt/homebrew/var/postgres start