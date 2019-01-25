start:
	@docker-compose up -d

stop:
	@docker-compose down

configure:
	@chmod 777 ./volumes/pgsqldb
	@chmod 777 ./volumes/teamcity-persistence
	@chmod 777 ./volumes/teamcity-logs
	@chmod 777 ./volumes/teamcity-agent-0
	@chmod 777 ./volumes/teamcity-agent-1
	@chmod 777 ./volumes/teamcity-agent-2

clean:
	@touch ./volumes/pgsqldb/touchflag
	@rm -r ./volumes/pgsqldb/*
	@touch ./volumes/teamcity-persistence/touchflag
	@rm -r ./volumes/teamcity-persistence/*
	@touch ./volumes/teamcity-logs/touchflag
	@rm -r ./volumes/teamcity-logs/*
	@touch ./volumes/teamcity-agent-0/touchflag
	@rm -r ./volumes/teamcity-agent-0/*
	@touch ./volumes/teamcity-agent-1/touchflag
	@rm -r ./volumes/teamcity-agent-1/*
	@touch ./volumes/teamcity-agent-2/touchflag
	@rm -r ./volumes/teamcity-agent-2/*
