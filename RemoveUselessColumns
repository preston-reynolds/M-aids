(table_name as table) =>
let 
// Find Columns with the data type of Record or Table and remove those:
BadColumns = Table.ColumnsOfType(table_name,{Record.Type,Table.Type}),
GoodSchema = Table.RemoveColumns(table_name,BadColumns),

// For use in next steps, capture the column names
Headers = Table.ColumnNames(GoodSchema),

// Return a table where the values in all rows of each column are not null
NonEmptyColumns = Table.SelectColumns(
             GoodSchema,
             List.Select(Headers, each List.MatchesAny(Table.Column(GoodSchema, _), each _ <> null))),
			 
// Return a table where the distinct values within each column amount to more than 1. 			 
NonUniqueValueColumns =  Table.SelectColumns(
			NonEmptyColumns,
			List.Select(Headers, each List.NonNullCount(List.Distinct(Table.Column(GoodSchema,_)))> 1)),
			
// In the event that you have more columns that need to be removed, you can list all of parts of their name in this 
columnsToRemove = List.Select(
						Table.ColumnNames(NonUniqueValueColumns), 
						each Text.Contains(_, "sink") 
						or Text.Contains(_, "owning") 
						or Text.Contains(_, "created") 
						or Text.Contains(_, "modified") 
						or Text.Contains(_, "owner") 
						or Text.Contains(_, "version")
						or Text.Contains(_, "exchange")
						) ,

// Remove the columns identfied in the previous step						
cleanedData = Table.RemoveColumns(NonUniqueValueColumns, columnsToRemove) 
in
cleanedData
