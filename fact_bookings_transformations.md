let
    Source = Csv.Document(File.Contents("C:\Users\85212269\Documents\BAT BI Projects\Case Study Wajiha\hotel_bookings (1).csv"),[Delimiter=",", Columns=32, Encoding=1252, QuoteStyle=QuoteStyle.None]),
    #"Promoted Headers" = Table.PromoteHeaders(Source, [PromoteAllScalars=true]),
    #"Changed Type" = Table.TransformColumnTypes(#"Promoted Headers",{{"hotel", type text}, {"is_canceled", Int64.Type}, {"lead_time", Int64.Type}, {"arrival_date_year", Int64.Type}, {"arrival_date_month", type text}, {"arrival_date_week_number", Int64.Type}, {"arrival_date_day_of_month", Int64.Type}, {"stays_in_weekend_nights", Int64.Type}, {"stays_in_week_nights", Int64.Type}, {"adults", Int64.Type}, {"children", Int64.Type}, {"babies", Int64.Type}, {"meal", type text}, {"country", type text}, {"market_segment", type text}, {"distribution_channel", type text}, {"is_repeated_guest", Int64.Type}, {"previous_cancellations", Int64.Type}, {"previous_bookings_not_canceled", Int64.Type}, {"reserved_room_type", type text}, {"assigned_room_type", type text}, {"booking_changes", Int64.Type}, {"deposit_type", type text}, {"agent", type text}, {"company", type text}, {"days_in_waiting_list", Int64.Type}, {"customer_type", type text}, {"adr", type number}, {"required_car_parking_spaces", Int64.Type}, {"total_of_special_requests", Int64.Type}, {"reservation_status", type text}, {"reservation_status_date", type date}}),
    #"Replaced Value" = Table.ReplaceValue(#"Changed Type",null,0,Replacer.ReplaceValue,{"children"}),
    #"Replaced Value1" = Table.ReplaceValue(#"Replaced Value","NULL","unknown",Replacer.ReplaceText,{"country"}),
    #"Replaced Value2" = Table.ReplaceValue(#"Replaced Value1","NULL","0",Replacer.ReplaceText,{"agent"}),
    #"Replaced Value3" = Table.ReplaceValue(#"Replaced Value2","NULL","0",Replacer.ReplaceText,{"company"}),
    #"Added Custom" = Table.AddColumn(#"Replaced Value3", "total_nights", each [stays_in_weekend_nights] + [stays_in_week_nights]),
    #"Added Custom1" = Table.AddColumn(#"Added Custom", "total_guests", each [adults]+[children]+[babies]),
    #"Added Custom2" = Table.AddColumn(#"Added Custom1", "total_revenue", each [adr]*[total_nights]),
    #"Changed Type2" = Table.TransformColumnTypes(#"Added Custom2",{{"total_revenue", type number}}),
    #"Filtered Rows" = Table.SelectRows(#"Changed Type2", each [total_guests] = 0),
    #"Filtered Rows1" = Table.SelectRows(#"Filtered Rows", each [adr] >= 0),
    #"Added Conditional Column" = Table.AddColumn(#"Filtered Rows1", "lead_time_bucket", each if [lead_time] <= 30 then "lead_time ≤ 30" else if [lead_time] <= 90 then "≤ 90" else if [lead_time] <= 180 then "≤ 180" else if [lead_time] <= 365 then "≤ 365" else 365),
    #"Removed Columns" = Table.RemoveColumns(#"Added Conditional Column",{"meal"}),
    #"Added Index" = Table.AddIndexColumn(#"Removed Columns", "Index", 1, 1, Int64.Type),
    #"Renamed Columns" = Table.RenameColumns(#"Added Index",{{"Index", "booking_id"}}),
    #"Duplicated Column" = Table.DuplicateColumn(#"Renamed Columns", "arrival_date_month", "arrival_date_month - Copy"),
    #"Changed Type1" = Table.TransformColumnTypes(#"Duplicated Column",{{"arrival_date_month - Copy", type date}}),
    #"Extracted Month" = Table.TransformColumns(#"Changed Type1",{{"arrival_date_month - Copy", Date.Month, Int64.Type}}),
    #"Removed Columns1" = Table.RemoveColumns(#"Extracted Month",{"arrival_date_month - Copy"}),
    #"Added Custom3" = Table.AddColumn(#"Removed Columns1", "Custom", each #date(
    [arrival_date_year],
    Date.Month(Date.FromText("1 " & [arrival_date_month] & " 2020")),
    [arrival_date_day_of_month]
)),
    #"Renamed Columns1" = Table.RenameColumns(#"Added Custom3",{{"Custom", "arrival_date"}})
in
    #"Renamed Columns1"