function cf_fe($token, $mail, $zonetag, $timestart)
{
    $timenw = time();
    $timestart = date("c", $timestart + 0.0001);
    $limit = 1000;
    $timeend = date("c", $timenw);
    $url = "https://api.cloudflare.com/client/v4/graphql";
    $data_arr = [
        "query" => 'query ListFirewallEvents($zoneTag: string, $filter: FirewallEventsAdaptiveFilter_InputObject) 
              {
                viewer {
                  zones(filter: { zoneTag: $zoneTag }) {
                    firewallEventsAdaptive(
                      filter: $filter
                      limit: ' . $limit . '
                      orderBy: [datetime_ASC]
                    ) {
                      action
                      clientAsn
                      clientCountryName
                      clientIP
                      clientRequestPath
                      clientRequestQuery
                      datetime
                      source
                      userAgent
                    }
                  }
                }
              }',
        "variables" => [
            "zoneTag" => "$zonetag",
            "filter" => [
                "datetime_geq" => $timestart,
                "datetime_leq" => $timeend,
            ],
        ],
    ];
    $ch = curl_init();
    $header = Array(
        "X-Auth-Email: $mail",
        "Authorization: Bearer $token",
        "Content-Type: application/json"
    );
    $data_string = json_encode($data_arr);
    curl_setopt($ch, CURLOPT_URL, $url);
    curl_setopt($ch, CURLOPT_HTTPHEADER, $header);
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
    curl_setopt($ch, CURLOPT_CUSTOMREQUEST, 'POST');
    curl_setopt($ch, CURLOPT_POSTFIELDS, $data_string);
    $data = curl_exec($ch);
    curl_close($ch);
    $json = json_decode($data, true);
    $answcf = $json['data']['viewer']['zones'][0]['firewallEventsAdaptive'];
    foreach ($answcf as $key => $item) $answcf[$key]['datetime'] = date('c', strtotime($item['datetime']) + 60 * 60 * 3);
    $json['data']['viewer']['zones'][0]['firewallEventsAdaptive'] = $answcf;
    return $json;
}
