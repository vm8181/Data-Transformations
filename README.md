Custom table = 
VAR previous_month =
    MONTH ( TODAY () ) - 1
VAR avg_1to7 =
    SUMMARIZE (
        FILTER (
            campaign_report,
            campaign_report[campaignStatus] = "enabled"
                && MONTH ( campaign_report[Date] ) = previous_month
                && DAY ( campaign_report[Date] ) <= 7
                && DAY ( campaign_report[Date] ) >= 1
        ),
        campaign_report[Date],
        campaign_report[Category],
        "Sales", DIVIDE(SUM(campaign_report[Sales]),7), "Identifier", 0 
    )
VAR previous_two_days =
SUMMARIZE (
    FILTER (
        'campaign_report',
        campaign_report[Date]
            = TODAY () - 4
            || campaign_report[Date]
                = TODAY () - 5
                && campaign_report[campaignStatus] = "enabled"
    ),
    campaign_report[Date],
    campaign_report[Category],
    "Sales", DIVIDE(SUM(campaign_report[Sales]),2), "Identifier", 1
)
VAR previous_days =
SUMMARIZE (
    FILTER (
        'campaign_report',
        campaign_report[Date]
            >= TODAY () - 4
            && campaign_report[campaignStatus] = "enabled"
    ),
    campaign_report[Date],
    campaign_report[Category],
    "Sales", SUM ( campaign_report[Sales] ), "Identifier", 2
)
VAR result = 
    UNION(avg_1to7, previous_two_days, previous_days)
RETURN
 result
