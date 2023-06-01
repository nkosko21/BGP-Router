In this file, you should describe your high-level approach, the challenges you faced, a list of properties/features of your design that you think is good, and an overview of how you tested your code.

Our high level approach to this project was to first have code in run to parse the four different message types, 'update', 'dump', 'data', and 'withdraw' messages, each with a respective method that is called. 

	Within the update method, the message is parsed in parse_update() where the new message is created and passed to update_table() and finally forward_message(). In update_table the peer field is changed and the update is either added to the forwarding table or aggregated. The message is then forwarded using forward_message(), where messages are forwarded to all routers (if the source is a customer), and dropped if the source is not.

	Within the dump() method, the forwarding table is sent to the the given destination.

	Within the data() method, the given data and source is pass onto send_data(). In send_data(), best_dest_match() is called to find the best router to send the information to based on the destination IP. If there is no route, best_dest_match() returns "no route". When there are multiple routers available within the given IP range, a tiebreaker system is called that breaks ties based on highest prefix matching, then highest localprefs, then if the selfOrigin is true, then the lowest ASPath, then the best origin, and finally if there continues to be a tie then best_dest_match() returns the Router with the lowest IP. Once the IP is found, if the source is a customer the message is sent, otherwise it is dropped.

	Within the withdraw() method, the given route is withdrawn and the forwarding table is disaggregated as necessary.

The biggest challenge with this project was route aggregation and making best_dest_match more efficient and shorter. The logic and implementation behind route aggregation was difficult to get down and best_dest_match was a 150> line function before we simplified it. In addition to this, keeping track of our various helper functions such as dec_to_bin or string_ip_to_binary was difficult but necessary as abstracting them made the code run much more efficiently. We also faced many challenges with aliasing in our aggregation and disaggregation methods, which we solved by copying many variables before sending them.

Some features we thought we designed well were:
	the fact binary was represented as a list throughout the code
	our helper functions
	best_dest_matching()'s efficiency
	aggregate_tables()'s logic

When creating the program, we tested our code thoroughly with the given test config files and printing various statements during the simulation. These statements helped us gain an understanding of how data was being passed around and where our code was going wrong.