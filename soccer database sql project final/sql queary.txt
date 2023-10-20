create database european_soccer;
use european_soccer;
select * from player_attributes;
select * from league;



# 1) How many teams played in each league during each season?



SELECT l.name, m.season, COUNT(DISTINCT m.id) AS num_teams
FROM league l
JOIN matches m ON l.id = m.league_id
GROUP BY l.name, m.season
ORDER BY l.name, m.season;
SELECT l.country,l.name AS league_name,m.season,COUNT(DISTINCT t.team_id) AS num_teams
FROM league l
JOIN matches m ON l.id = m.league_id
JOIN team t ON (t.team_id = m.home_team_id OR t.team_id = m.away_team_id)
GROUP BY l.country, l.name, m.season
ORDER BY l.country, l.name, m.season;


#2) What was the count and average goals scored in each league during each season?
SELECT l.country, l.name AS league_name,m.season,COUNT(m.id) AS match_count,AVG(m.home_team_goal + m.away_team_goal) AS average_goals
FROM league l
JOIN matches m ON l.id = m.league_id
GROUP BY l.country, l.name, m.season
ORDER BY l.country, l.name, m.season;


#3) How was the performance (home win, home tiem home loss, away win, away loss, away tie) of teams in each league during each season?

SELECT
    l.name,
    l.name AS league_name,
    m.season,
    SUM(CASE
        WHEN m.home_team_goal > m.away_team_goal THEN 1
        ELSE 0
    END) AS home_wins,
    SUM(CASE
        WHEN m.home_team_goal = m.away_team_goal THEN 1
        ELSE 0
    END) AS home_ties,
    SUM(CASE
        WHEN m.home_team_goal < m.away_team_goal THEN 1
        ELSE 0
    END) AS home_losses,
    SUM(CASE
        WHEN m.away_team_goal > m.home_team_goal THEN 1
        ELSE 0
    END) AS away_wins,
    SUM(CASE
        WHEN m.away_team_goal < m.home_team_goal THEN 1
        ELSE 0
    END) AS away_losses,
    SUM(CASE
        WHEN m.away_team_goal = m.home_team_goal THEN 1
        ELSE 0
    END) AS away_ties
FROM league l
JOIN matches m ON l.id = m.id
GROUP BY l.name, l.name, m.season
ORDER BY l.name, l.name, m.season;


#4) How to quantify the performance in terms of match results?
SELECT id,home_team_goal,
    away_team_goal,
    CASE
        WHEN home_team_goal > away_team_goal THEN 'win'
        WHEN home_team_goal = away_team_goal THEN 'draw'
        ELSE 'loss'
    END AS match_result
FROM matches;

#5) Which players had the most penalties?
SELECT pa.id,p.player_name,SUM(pa.penalties) AS total_penalties
FROM player_attributes pa
JOIN player p ON pa.id = p.id
GROUP BY pa.id, p.player_name
ORDER BY total_penalties DESC;


# 6) What player attributes lead to the most potential 
SELECT pa.id, pa.acceleration, pa.stamina, pa.potential
FROM player_attributes pa
JOIN player p ON pa.id = p.id;


# 7) Correlation between some player attributes and player potential.
SELECT pa.finishing,pa.potential
FROM player_attributes pa
JOIN player p ON pa.id = p.id;
